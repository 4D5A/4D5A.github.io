---
layout: post
title: "Setting Up Apache Guacamole on a Proxmox LXC Container (Ubuntu 24.04-2 → Tomcat 10 → MariaDB)"
categories: [homelab, lxcs]
tags: [container, linux, proxmox, apache-guacamole, tomcat, mariadb, java, rdp]
after-content: [disclaimer-notice.html]
---

# Setting Up Apache Guacamole on a Proxmox LXC Container (Debian 10 → Tomcat 10 → MariaDB)

## Introduction

This document captures a full end-to-end installation of Apache Guacamole inside a Proxmox LXC container. The goal was to deploy a self-hosted remote access gateway supporting RDP (and later extensible to SSH/VNC), backed by MariaDB and running on Tomcat 10.

The process required resolving several compatibility issues across:

- Java (JDK + build tools)
- Maven build system
- NodeJS frontend tooling
- Tomcat 10 Jakarta EE migration
- XRDP session stability on the target Kali Linux host

By the end, Guacamole was fully operational and able to connect reliably to remote desktop sessions.

---

## 1. Proxmox LXC Container Setup

A Debian 10 (Buster) template was used as the base container.

**Container configuration:**
- Hostname: `guacamole`
- CPU: 1 core
- RAM: 1024MB

---

## 2. Installing System Dependencies

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

## 3. Building guacd

```bash
git clone https://github.com/apache/guacamole-server.git
cd guacamole-server

autoreconf -fi
./configure --with-systemd-dir=/usr/local/lib/systemd/system
make
make install
ldconfig
```

## 4. Tomcat 10 Deployment Issue

```bash
wget -O guacamole.war \
"https://apache.org/dyn/closer.lua/guacamole/1.6.0/binary/guacamole-1.6.0.war?action=download"

cp guacamole.war /var/lib/tomcat10/webapps/
```
Problem

Tomcat 10 requires Jakarta EE (jakarta.*), but Guacamole 1.6.0 uses javax.*.

## 5. Java Setup

```bash
update-alternatives --config java
update-alternatives --config javac

export JAVA_HOME=/usr/lib/jvm/java-11-openjdk-amd64
export PATH=$JAVA_HOME/bin:$PATH
```

## 6. Building guacamole-client

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

## 7. NodeJS Requirement

NodeJS is required for:

```bash
npx jsdoc -c jsdoc-conf.json
```
This step is optional and can be skipped for production builds.

## 8. Jakarta Migration (Tomcat 10 Fix)
```bash
wget https://archive.apache.org/dist/tomcat/jakartaee-migration/v1.0.8/binaries/jakartaee-migration-1.0.8-bin.tar.gz
tar -xvzf jakartaee-migration-1.0.8-bin.tar.gz
cd jakartaee-migration-1.0.8

java -jar lib/jakartaee-migration-1.0.8.jar \
~/guacamole-client-1.6.0/guacamole/target/guacamole-1.6.0.war \
~/guacamole-client-1.6.0/guacamole/target/guacamole-jakarta.war
```

## 9. MariaDB Setup

configure the database
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

## 10. Authentication Extension

```bash
cp guacamole-auth-jdbc-mysql-1.6.0.jar /etc/guacamole/extensions/
```

Drivers:

```bash
cp mysql-connector-j-8.4.0.jar /etc/guacamole/lib/
cp /usr/share/java/mariadb-java-client.jar /etc/guacamole/lib/
```

## 11. Configuration

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

## 12. Services

```bash
systemctl restart guacd
systemctl restart tomcat10
```

Bind guacd:
```ini
[Service]
ExecStart=
ExecStart=/usr/local/sbin/guacd -b 127.0.0.1 -f
```

## 13. Create a guacamole user

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

## 14. RDP Issue (Kali Linux)
Symptoms:

Brief connection
Immediate disconnect

Logs:

```bash
Internal RDP client disconnected
Connection closed
```

## 15. XRDP Fix

```bash
nano /etc/xrdp/startwm.sh
```

```bash
#!/bin/sh

if [ -r /etc/profile ]; then
  . /etc/profile
fi

startxfce4
```

```/bash
sudo systemctl restart xrdp xrdp-sesman
```