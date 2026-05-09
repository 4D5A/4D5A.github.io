---
layout: post
title: "Lab 2: Create an Ubuntu LXC"
categories: [labs, proxmox]
tags: [linux, proxmox]
after-content: [disclaimer-notice.html]
---
This is lab number 2 in this series. If you have not already completed the previous lab, please start at [Lab 1: Create a proxmox server](https://4d5a.github.io/2026-05-09-build-a-proxmox-server/).

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

    Now you have the LXC Template for Ubuntu 24.04. You will need this to create a Proxmox LXC that emulates Ubuntu 24.04.

    Let's create the Ubuntu 24.04 LXC.

12. Right click on your proxmox node (The default name is "pve".)
13. Click "Create CT".

    Now comes the fun part. Now you need to configure the virtual hardware and choose which linux distribution the LXC will emulate (we will use Ubuntu 24.04 for this lab).

    Use the following settings:

    General
    - Node: pve
    - CT ID: 100
    - Hostname: lab1-ubuntu
    - Unprivileged container: Checked
    - Nesting: Unchecked
    - Resource Pool: Leave blank
    - Password: **Use a unique, complex password. This will be the "root" password for your LXC.**
    - Confirm password: Re-enter your unique, complex password.
    
    Template
    - Storage: Choose where your LXCs virtual storage will be located
    - Template: ubuntu-24.04-standard_24.04-2_amd64.tar.zst
    
    Disks
    - Storage:
    - Disk size (GiB):
    
    CPU
    - Cores: 1
    - CPU Limit: unlimited
    - CPU Units: 100
    
    Memory
    - Memory (MiB): 512
    - Swap (MiB): 512
    
    Network
    - Name: eth0
    - MAC address: auto
    - Bridge: vmbr0
    - VLAN Tag: no VLAN
    - Firewall: Checked
    - IPv4: DHCP
    
    
14. Click "Confirm".
15. Click "Finish".
16. Expand your node.
17. Click your LXC.
18. Click "Console".
19. Click "Start".
20. Login using the username root and the LXC password you chose.
21. Congratulations, you are logged into a proxmox LXC emulating Ubuntu 24.04. Let's update Ubuntu 24.04.
22. Run the commands below.

~~~bash
apt get update
apt get upgrade -y
~~~

    >Hint: ```apt get update``` will check for updates for the programs installed on your linux system. ```apt get upgrade -y``` will install them without being prompted for confirmation. You can combine these commands by entering **apt get update && apt get upgrade -y**.

23. Run the command ```reboot now!``` to reboot your LXC.
24. Next let's install an nginx web server with the command ```apt get install nginx -y```.
25. Run the command ```ip a``` to find your LXCs IP address.
26. On your management system, browse to http://your-lxcs-ip-address.
27. Congratulations, you now have an LXC emulating Ubuntu 24.04 that is running an nginx web server.

>Tip: Be aware that if you did not connect the LXC to a VLAN, it may be running on the same network your management system is connected to. While this may simplify access, it is important that you properly secure all lab infrastructure including your proxmox server and LXCs. A security best practice is to place servers in their own VLAN. Infrastructure management interfaces should be in a seperate VLAN. While you might think threat actors are not interested in attacking your lab infrastructure, there are examples of that taking place. Because each network is a unique environment, recommending specific network designs or security configurations is outside the scope of these labs. Please ensure you are properly securing your environment as you alone are responsible for your network security.

[https://www.pcmag.com/news/lastpass-employee-couldve-prevented-hack-with-a-software-update](https://www.pcmag.com/news/lastpass-employee-couldve-prevented-hack-with-a-software-update)

[https://www.cisa.gov/news-events/alerts/2023/11/29/cisa-releases-first-secure-design-alert](https://www.cisa.gov/news-events/alerts/2023/11/29/cisa-releases-first-secure-design-alert)

Here is a list of TryHackMe rooms that offer introductory linux training.

[https://tryhackme.com/room/linuxfundamentalspart1](https://tryhackme.com/room/linuxfundamentalspart1)
[https://tryhackme.com/room/linuxfundamentalspart2](https://tryhackme.com/room/linuxfundamentalspart2)
[https://tryhackme.com/room/linuxfundamentalspart3](https://tryhackme.com/room/linuxfundamentalspart3)
