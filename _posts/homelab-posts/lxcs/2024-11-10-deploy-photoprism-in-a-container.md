---
layout: post
title: Deploy PhotoPrism in a container
categories: [homelab, lxcs]
tags: [container, linux, photoprism, proxmox]
after-content: [disclaimer-notice.html]
---


~~~
apt update
apt install docker-ce
wget https://dl.photoprism.app/docker/compose.yaml
~~~

## Add Docker's official GPG key:
~~~
sudo apt-get update
sudo apt-get install ca-certificates curl
sudo install -m 0755 -d /etc/apt/keyrings
sudo curl -fsSL https://download.docker.com/linux/debian/gpg -o /etc/apt/keyrings/docker.asc
sudo chmod a+r /etc/apt/keyrings/docker.asc
~~~

## Add the repository to Apt sources:
~~~
echo \
  "deb [arch=$(dpkg --print-architecture) signed-by=/etc/apt/keyrings/docker.asc] https://download.docker.com/linux/debian \
  $(. /etc/os-release && echo "$VERSION_CODENAME") stable" | \
  tee /etc/apt/sources.list.d/docker.list > /dev/null
apt-get update
~~~

## Install Docker
~~~
apt-get install docker-ce docker-ce-cli containerd.io docker-buildx-plugin docker-compose-plugin
~~~

## Install CIFS utils (if you want to mount an SMB share for PhotoPrism to store pictures)

```apt-get install cifs-utils```

## Create a directory to use as a mountpoint for the SMB share
```mkdir /media/appdata```

## Create a file to store your SMB credentials (this allows you to configure /etc/fstab to mount your SMB share without placing your SMB credentials in /etc/fstab)
```nano /root/.smb```

~~~
username=YOUR_USERNAME
password=YOUR_PASSWORD
~~~

## Add settings to mount your SMB share in /etc/fstab
```nano /etc/fstab```

~~~
//YOURIP/home/appdata /media/appdata cifs credentials=/root/.smb,uid=0,gid=0,dir_mode=0777,file_mode=0777,users,rw,iocharset=utf8,noperm 0 0
~~~

## Mount your SMB share
```mount -a```

## Configure PhotoPrism Volume Mounts
Open docker-compose.yml in a text editor. You should either be in the directory where your PhotoPrism "docker-compose.yml" is stored, or modify the following command to open it using its full path.

```nano docker-compose.yml```

~~~
- "/media/appdata/originals:photoprism/originals"
- "/media/appdata/import:photoprism/import"
~~~

For more information, please refer to [https://www.photoprism.app/plus/kb/volumes](https://www.photoprism.app/plus/kb/volumes).

## Start the docker container for PhotoPrism.
```docker compose up -d```