---
layout: post
title: How to bulk edit DNS records with Pi-hole
categories: [homelab, dns]
tags: [linux, pihole]
after-content: [disclaimer-notice.html]
---

~~~
nano /etc/pihole/custom.list
killall pihole-FTL
pihole restartdns
~~~