---
layout: post
title: Manually start Calibre-Web
categories: [homelab, lxcs]
tags: [calibreweb, container, linux, proxmox]
after-content: [disclaimer-notice.html]
---

If you have not configured your LXC or VM to automatically start Calibre-Web or if you need to manually start Calibre-Web after it has stopped, you need to run this command (This may need to be modified to apply to where you installed Calibre-Web on your LXC.)

```nohup /opt/calibre-web/venv/bin/cps```

Information about installing Calibre-Web including on how to configure your LXC or VM to automatically start Calibre-Web can be found at [Install Calibre Web in a Container](/2024-09-15-install-calibre-web-in-a-container)