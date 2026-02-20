---
layout: post
title: Manually start PhotoPrism
categories: [homelab, lxcs]
tags: [container, linux, photoprism, proxmox]
after-content: [disclaimer-notice.html]
---

If you have not configured your LXC or VM to automatically start PhotoPrism or if you need to manually start PhotoPrism after it has stopped, you need to run this command (This may need to be modified to apply to where you installed Calibre-Web on your LXC.)
~~~
start photoprism
cd /home/docker/photoprism
docker compose up -d
~~~