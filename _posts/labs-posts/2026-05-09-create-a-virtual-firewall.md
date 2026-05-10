---
layout: post
title: "Lab 3: Create a virtual firewall"
categories: [labs, proxmox]
tags: [linux, proxmox]
after-content: [disclaimer-notice.html]
---
This is lab number 3 in this series. If you have not already completed the previous lab, please start at [Lab 2: Create an Ubuntu LXC](https://4d5a.github.io/2026-05-09-create-an-ubuntu-lxc/).

1. Open [https://opnsense.org/download/](https://opnsense.org/download/)
2. Pick "DVD" from the "Select the image type" dropdown box.
3. Right click the "Download OPNsense" button.
4. Click "Copy link".
5. Login to your proxmox server.
6. Click "Datacenter".
7. Click "Storage".
8. Identify which storage path includes "ISO Image" in its "Content" column
9. On the left side of the screen, expand your node.
10. Scroll down until you see the storage path you identified in step 4.
11. Click the storage.
12. Click "ISO Images".
13. Click "Download from URL".
14. Right click in the "URL:" textbox.
15. Click "Paste".
16. Click "Query URL".
17. Click "Download".
18. Right click on your proxmox node (The default name is "pve".)
19. Click "Create VM".

    Use the following settings:

    * General
        * Node: pve
        * VM ID: 200
        * Name: lab3-opnsense
        * Start at boot: Checked
    * OS
        * Use CD/DVD disc image file (iso): Yes
            * Storage: Pick the storage path where "ISO Image" are stored. This is the same storage path as the one identified in step 8 above.
            * ISO Image: OPNsense-26.1.6-dvd-amd64.iso
    * Disks
        * Storage: Choose where your VM virtual drive will be located
        * Disk size (GiB): 50
    * CPU
        * Cores: 1
        * CPU Limit: unlimited
        * CPU Units: 100
    * Memory
        * Memory (MiB): 4096
        * Minimum memory (MiB): 4096
    * Network
        * Name: eth0
        * MAC address: auto
        * Bridge: vmbr0
        * VLAN Tag: no VLAN (this will be your WAN interface)
        * Firewall: Checked
        * IPv4: DHCP

20. Click "Confirm".
21. Click "Finish".
    Your VM is created but it only has a single network interface. It needs to have at least two interfaces so one can be configured as the "WAN" (which we will connect to our existing network) and the other, the "LAN" (which will be a seperate routed network to which we will connect other proxmox LXCs and VMs.)
22. Expand your node.
23. Click your "lab3-opnsense" VM.
24. Click "Hardware".
25. Click "Add".
26. Click "Network Device".

    Use the following settings:

    * Bridge: vmbr0
    * VLAN Tag: 20
    * Firewall: Checked
    * Model: VirtIO (paravitualized) 

27. Click "Add".
28. Click "Options".
29. Click "Boot Order".
30. Click "Edit".
31. Find your virtual DVD drive and move it to the top of the Boot Order.
32. Click "OK".
33. Click "Console".
34. Click "Start".
35. Boot to the OPNSense ISO.
36. Install OPNSense.

    After you install OPNSense, you should have the following network topology on your proxmox server.

    ```text
    +------------------------+
    | VLAN 1 (Home Network)  |
    +------------------------+
                |
                v
    +------------------------+
    | Proxmox Server         |
    | (Connected on VLAN 1)  |
    +------------------------+
                |
                v
    +----------------------------------+
    | lab3-opnsense                    |
    |                                  |
    |  eth0 NIC  ---> VLAN 1           |
    |  eth1 NIC  ---> VLAN 20          |
    +----------------------------------+
    ```
    
38. Login to lab3-opnsense through the VM's Console. The default username is "root" and the default password is "opnsense".
39. From the console for lab3-opnsense, choose option "7" for "Ping host" and verify that you can ping one of the IP addresses of Google's Public DNS Resolvers, 8.8.8.8. If that is successful you are ready to verify that your OPNSense firewall can resolve DNS queries. Since OPNSense does not have nslookup or dig installed by default, choose option "7" for "Ping host" again and verify that you can ping google.com. 
40. Verify the eth0 IP address for your OPNSense VM and either set a static IP address or create a DHCP Reservation for the one eth0 received from DHCP.

You have installed an OPNSense firewall! In the next lab, we will create a Windows VM and connect it to the "LAN" network on the OPNSense firewall.

Here is a list of TryHackMe rooms that introduce networking concepts.

[https://tryhackme.com/room/introtonetworking](https://tryhackme.com/room/introtonetworking)
[https://tryhackme.com/room/networkservices](https://tryhackme.com/room/networkservices)
[https://tryhackme.com/room/networkservices2](https://tryhackme.com/room/networkservices2)
[https://tryhackme.com/room/networkingconcepts](https://tryhackme.com/room/networkingconcepts)
[https://tryhackme.com/room/networkingessentials](https://tryhackme.com/room/networkingessentials)
[https://tryhackme.com/room/networkingcoreprotocols](https://tryhackme.com/room/networkingcoreprotocols)
[https://tryhackme.com/room/networkingsecureprotocols](https://tryhackme.com/room/networkingsecureprotocols)

After you understand the basics of networking, you can start learning about network services such as firewalls, stateful packet inspection, IDS/IPS, URL filtering, application filtering, and SSL Inspection.

Here are two TryHackMe rooms that introduce firewalls and IDS/IPS.

[https://tryhackme.com/room/idsfundamentals](https://tryhackme.com/room/idsfundamentals)
[https://tryhackme.com/room/firewallfundamentals](https://tryhackme.com/room/firewallfundamentals)