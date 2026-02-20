---
layout: post
title: Reduce disk space used by uptime-kuma
categories: [homelab, lxcs]
tags: [container, linux, proxmox, uptime-kuma]
after-content: [disclaimer-notice.html]
---
1. Click "Clear all statistics" and wait until kuma.db-wal stops growing.
2. Click "Shrink database"

Run these commands to trim kuma.db-wal and reindex the sqlite database.
~~~
sqlite3 ./data/kuma.db "PRAGMA wal_checkpoint=TRUNCATE"
sqlite3 ./data/kuma.db "REINDEX"
~~~




