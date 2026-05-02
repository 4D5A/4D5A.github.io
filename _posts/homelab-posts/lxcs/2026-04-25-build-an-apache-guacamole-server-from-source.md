---
layout: post
title: Setting Up Apache Guacamole on a Proxmox LXC Container using Ubuntu 24.04-2, Tomcat 10 and MariaDB)
categories: [homelab, lxcs]
tags: [container, linux, proxmox, apache-guacamole, tomcat, mariadb, java, rdp]
after-content: [disclaimer-notice.html]
---

## Introduction

This started as an effort to use Guacamole to provide browser-based access to a VM located in a different VLAN with RDP and to use an existing haproxy reverse proxy server instead of using Guacamole's nginx reverse proxy server.

PC (VLAN 1) -> haproxy eth0 (VLAN 1) -> haproxy eth1 (VLAN 50) -> Guacamole eth0 (VLAN 50) -> Guacamole eth1 (VLAN 10) -> VM accessed with RDP (VLAN 10)

What I expected would be an hour of work or less, ended up taking several hours as I repeatedly encountered obstacles.

While Apache provides a compiled version of guacamole-client, they do not offer a compile version of guacamole-server and both are necessary for this to work. [https://guacamole.apache.org/releases/1.6.0/](https://guacamole.apache.org/releases/1.6.0/) Obstacle number one.

No problem, there is documentation on installing guacamole-client and guacamole-server through Docker. [https://guacamole.apache.org/doc/gug/guacamole-docker.html](https://guacamole.apache.org/doc/gug/guacamole-docker.html). I chose to use Docker Compose with Portainer to deploy the stack. You can review the Docker Compose file at [https://github.com/4D5A/docker/blob/main/compose-configurations/apache-guacamole/docker-compose.yml](https://github.com/4D5A/docker/blob/main/compose-configurations/apache-guacamole/docker-compose.yml).

There is an "Important" note about setting up Postgres or MySQL/mariadb in [https://guacamole.apache.org/doc/gug/guacamole-docker.html](https://guacamole.apache.org/doc/gug/guacamole-docker.html)


>If using PostgreSQL or MySQL for authentication, you will need to initialize the database manually. Guacamole will not automatically create its own tables, but SQL scripts are provided to do this.

The importance of this information cannot be understated. It is easy to read past and one does at one's own peril (or at least at the risk of wasting hours of time). As the note states, when you deploy the stack, Guacamole will create its database as shown in the Docker Compose file, but "will not automatically create its own tables." Because the tables are not created, the database schema is also missing. Thanks to this [Deploy Apache Guacamole with Docker Compose article](https://computingforgeeks.com/deploy-guacamole-docker-compose/), I found the information I needed to initialize the database. Apache also provides instructions for database intialization at the following URIs.

|Database|URI|
|MariaDB or MySQL|[https://guacamole.apache.org/doc/gug/mysql-auth.html](https://guacamole.apache.org/doc/gug/mysql-auth.html)|
|PostgreSQL|[https://guacamole.apache.org/doc/gug/postgresql-auth.html](https://guacamole.apache.org/doc/gug/postgresql-auth.html)|
|SQL Server|[https://guacamole.apache.org/doc/gug/sqlserver-auth.html](https://guacamole.apache.org/doc/gug/sqlserver-auth.html)|

The purpose of this command is to create a directory for docker to extract the database schema from the guacamole Docker image, save it in /opt/guacamole/init, and use the .sql file to create the database schema in the Postgres or MySQL/mariadb database.

```bash
sudo mkdir -p /opt/guacamole/init
cd /opt/guacamole
sudo docker run --rm guacamole/guacamole /opt/guacamole/bin/initdb.sh --mysql | sudo tee init/01-schema.sql > /dev/null
```
Whether you need to include "sudo" in the above docker command depends on your Docker server configuration, if you are using root, a non-root user with sudo privileges, rootless, etc.

Another important piece of information is the initialization must be done before you deploy the stack. If you deploy the stack first, you will need to delete the stack, initialize the database, and re-create the stack. Alternatively, you can stop the Postgres or MySQL/mariadb container in the stack, delete the database's docker volume, initialize the database, and re-deploy the stack.

The next obstacle was that Guacamole does not include any users so you will not be able to login. At this point, I just wanted to be able to login, so I logged into MySQL and created the user.

```sql
mysql -u guacadmin -p guacamole_db

INSERT INTO guacamole_entity (name, type) VALUES ('admin', 'USER');
INSERT INTO guacamole_user (entity_id, password_hash, password_salt, password_date)
VALUES (
  LAST_INSERT_ID(),
  UNHEX(SHA2('guacadmin', 256)),
  NULL,
  NOW()
);
```

This allowed me to login, but because my stack was running on a Docker server in VLAN 50 and outbound traffic is translated from the stack's network to the Docker server's ethernet adapter, there wasn't a good method of accessing the stack through my existing haproxy. Even if I solved that issue, I would need to make it possible for Guacamole to connect to the VM in VLAN 10.

After thinking through the issue, I determined the best answer would be to run Guacamole (guacamole-client, guacamole-server, and mariadb) on a Proxmox LXC. That way I could tag the LXC's eth0 ethernet adapter as VLAN 50, access it through the existing haproxy reverse proxy, and add a second ethernet adapter (eth1) that would be configured to connect to VLAN 10.

## Building guacamole from source

This document captures a full end-to-end installation of Apache Guacamole inside a Proxmox LXC container. The goal was to deploy a self-hosted remote access gateway supporting RDP (and later extensible to SSH/VNC), backed by MariaDB and running on Tomcat 10.

The process required resolving several compatibility issues across:

- Java (JDK + build tools)
- Maven build system
- Tomcat 10 Jakarta EE migration
- XRDP session stability on the target VM

By the end, Guacamole was fully operational and able to connect reliably to remote desktop sessions.

---

### 1. Proxmox LXC Container Setup

An Ubuntu 24.04-2 (Noble Numbat) template was used as the base container.

**Container configuration:**
- Hostname: `guacamole`
- CPU: 1 core
- RAM: 1024MB

---

### 2. Installing System Dependencies

```bash
apt update && apt upgrade -y

apt install git make software-properties-common \
libcairo2-dev libjpeg-turbo8-dev libpng-dev libtool-bin \
uuid-dev libavcodec-dev libavformat-dev libavutil-dev libswscale-dev \
freerdp2-dev libpango1.0-dev libssh2-1-dev libpulse-dev \
libssl-dev libvorbis-dev libwebp-dev autotools-dev \
maven tomcat10 openjdk-11-jdk \
mariadb-server mariadb-client libmariadb-java -y
```

Create user:
```bash
adduser user
usermod -aG sudo user
```

### 3. Building guacd

```bash
git clone https://github.com/apache/guacamole-server.git
cd guacamole-server

autoreconf -fi
./configure --with-systemd-dir=/usr/local/lib/systemd/system
make
make install
ldconfig
```

### 4. Tomcat 10 Deployment Issue

```bash
wget -O guacamole.war \
"https://apache.org/dyn/closer.lua/guacamole/1.6.0/binary/guacamole-1.6.0.war?action=download"

cp guacamole.war /var/lib/tomcat10/webapps/
```
Problem

Tomcat 10 requires Jakarta EE (jakarta.*), but Guacamole 1.6.0 uses javax.*.

### 5. Java Setup

```bash
update-alternatives --config java
update-alternatives --config javac

export JAVA_HOME=/usr/lib/jvm/java-11-openjdk-amd64
export PATH=$JAVA_HOME/bin:$PATH
```

### 6. Building guacamole-client

```bash
wget https://apache.org/dyn/closer.lua/guacamole/1.6.0/source/guacamole-client-1.6.0.tar.gz
tar -xzf guacamole-client-1.6.0.tar.gz
cd guacamole-client-1.6.0
```

Build fix (fast workaround)

```bash
mvn clean install -Dmaven.javadoc.skip=true -DskipTests
mvn package -Dmaven.javadoc.skip=true -DskipTests -pl '!guacamole-common-js'
```

### 7. Jakarta Migration (Tomcat 10 Fix)
```bash
wget https://archive.apache.org/dist/tomcat/jakartaee-migration/v1.0.8/binaries/jakartaee-migration-1.0.8-bin.tar.gz
tar -xvzf jakartaee-migration-1.0.8-bin.tar.gz
cd jakartaee-migration-1.0.8

java -jar lib/jakartaee-migration-1.0.8.jar \
~/guacamole-client-1.6.0/guacamole/target/guacamole-1.6.0.war \
~/guacamole-client-1.6.0/guacamole/target/guacamole-jakarta.war

sudo cp ~/guacamole-client-1.6.0/guacamole/target/guacamole-jakarta.war \
/var/lib/tomcat10/webapps/guacamole.war
```

### 8. MariaDB Setup

Configure the database
```sql
sudo mysql_secure_installation
sudo mysql -u root -p
CREATE DATABASE guacamole_db;
CREATE USER 'guacadmin'@'localhost' IDENTIFIED BY 'guacadmin';
GRANT ALL PRIVILEGES ON guacamole_db.* TO 'guacadmin'@'localhost';
FLUSH PRIVILEGES;
EXIT;
```

Import Schema
```bash
cat ~/guacamole-client-1.6.0/extensions/guacamole-auth-jdbc/modules/guacamole-auth-jdbc-mysql/schema/*.sql \
| mysql -u guacadmin -p guacamole_db
```

Create a guacamole user
```bash
mysql -u guacadmin -pYOURSECUREPASSWORD guacamole_db
```
The example mysql commands below adds a gualamole user with the credentials "guacadmin/guacadmin".
```sql
INSERT INTO guacamole_entity (name, type) VALUES ('admin', 'USER');
INSERT INTO guacamole_user (entity_id, password_hash, password_salt, password_date)
VALUES (
  LAST_INSERT_ID(),
  UNHEX(SHA2('guacadmin', 256)),
  NULL,
  NOW()
);
```

### 9. Authentication Extension

```bash
cp guacamole-auth-jdbc-mysql-1.6.0.jar /etc/guacamole/extensions/
```

Drivers:

```bash
cp mysql-connector-j-8.4.0.jar /etc/guacamole/lib/
cp /usr/share/java/mariadb-java-client.jar /etc/guacamole/lib/
```

### 10. Configuration

Change mysql-password from "YOURSECUREPASSWORD" to a secure password that you create.

```properties
guacd-hostname: localhost
guacd-port: 4822

mysql-hostname: localhost
mysql-port: 3306
mysql-database: guacamole_db
mysql-username: guacadmin
mysql-password: YOURSECUREPASSWORD

mysql-ssl-mode: disabled
```

### 11. Services

```bash
systemctl restart guacd
systemctl restart tomcat10
```

Bind guacd:

```sudo nano /usr/local/lib/systemd/system/guacd.service```

```ini
[Service]
User=user
Group=user
ExecStart=
ExecStart=/usr/local/sbin/guacd -b 127.0.0.1 -f
```

### 12. RDP Issue
Symptoms:

Brief connection
Immediate disconnect

Logs:

```bash
Internal RDP client disconnected
Connection closed
```

### 13. XRDP Fix
Edit the script xrdp uses to initiate the desktop environment.
```bash
nano /etc/xrdp/startwm.sh
```

Below is what worked for me.
```bash
#!/bin/sh

if [ -r /etc/profile ]; then
  . /etc/profile
fi

startxfce4
```
Restart the xrdp and xrdp session manager services.
```bash
sudo systemctl restart xrdp xrdp-sesman
```

### 14. Verify that everything is working

Login to Guacamole and connect to a VM.