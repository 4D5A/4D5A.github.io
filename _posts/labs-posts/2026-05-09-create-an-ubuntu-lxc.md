---
layout: post
title: Create an Ubuntu LXC
categories: [labs, proxmox]
tags: [linux, proxmox]
after-content: [disclaimer-notice.html]
---
This is lab number 2 in this series. If you have not already completed the previous lab, please start at [2026-05-09-build-a-proxmox-server](2026-05-09-build-a-proxmox-server).

1. Login to your proxmox server.
2. Click "Datacenter".
3. Click "Storage".
4. Identify which storage path includes "Container Template" in its "Content" column
5. On the left side of the screen, expand your node.
6. Scroll down until you see the storage path you identified in step 4.
7. Click the storage path.
8. Click "CT Templates".
9. Click the button labeled, "Templates".
10. In "Section: system", click "lxc ubuntu-24.04-standard 24.04-2 Ubuntu 24.04 Noble (standard).
11. Click "Download".

Now you have 