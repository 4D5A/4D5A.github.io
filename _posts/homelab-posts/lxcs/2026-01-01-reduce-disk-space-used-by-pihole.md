---
layout: post
title: Reduce disk space used by Pi-hole
categories: [homelab, lxcs]
tags: [container, linux, pihole, proxmox]
after-content: [disclaimer-notice.html]
---

~~~
rm /etc/pihole/pihole-FTL.db
pihole -a -p
~~~