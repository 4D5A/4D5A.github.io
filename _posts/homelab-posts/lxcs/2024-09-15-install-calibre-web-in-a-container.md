---
layout: post
title: Install Calibre Web in a Container
categories: [homelab, lxcs]
tags: [calibreweb, container, linux, proxmox]
after-content: [disclaimer-notice.html]
---

~~~
apt update -y && apt upgrade -y
apt install python3-pip python3-venv -y
adduser calibre
usermod -aG sudo calibre
~~~

Logout of root.

Login as calibre.

~~~
mkdir /home/calibre/library
chmod 700 /home/calibre/library
python3 -m venv venv
./venv/bin/python3 -m pip install calibreweb
~~~

```nano /etc/systemd/system/calibreweb.service```

~~~
[Unit]
Description=Calibre-Web

[Service]
Type=simple
User=calibre
ExecStart=/home/calibre/venv/bin/python3 /home/calibre/venv/bin/cps
WorkingDirectory=/home/calibre/venv/bin

[Install]
WantedBy=multi-user.target
~~~

```systemctl enable calibreweb```

```systemctl start calibreweb```

```lsof -nP -iTCP -sTCP:LISTEN```

```nano /etc/ssh/sshd_config```
Change ```PermitRootLogin without-password``` to ```PermitRootLogin yes```
CTRL + X
Y
ENTER
```service ssh restart```

Upload "metadata.db" to /home/calibre/library. If you already have a Calibre library database from Calibre, you can upload that. If you do not have a Calibre database file, or if you want to use a blank Calibre database for Calibre Web, you can download one from the [Calibre-Web GitHub Project](https://github.com/janeczku/calibre-web/blob/master/library/metadata.db).

```chown root:calibre /home/calibre/library/metadata.db```

```chmod 770 /home/calibre/library/metadata.db```