---
layout: post
title: "Lab 4: Create an Kali VM for TryHackMe"
categories: [labs, proxmox]
tags: [linux, proxmox]
after-content: [disclaimer-notice.html]
---
This is lab number 4 in this series. If you have not already completed the previous lab, please start at [Lab 3: Create a virtual firewall](https://4d5a.github.io/2026-05-09-create-a-virtual-firewall/).

1. Download the current version of the Kali Installer Image from [https://www.kali.org/get-kali/#kali-installer-images](https://www.kali.org/get-kali/#kali-installer-images).
2. Upload the ISO to your proxmox "ISO Image" storage.
3. Create a proxmox VM using the following settings.
    * General
        * Node: pve
        * VM ID: 201
        * Name: lab4-kali
        * Start at boot: Checked
    * OS
        * Use CD/DVD disc image file (iso): Yes
            * Storage: Pick the storage path where "ISO Image" are stored.
            * ISO Image: kali-linux-2026.1-installer-amd64.iso
    * Disks
        * Storage: Choose where your VM virtual drive will be located
        * Disk size (GiB): 100
    * CPU
        * Cores: 2
        * CPU Limit: unlimited
        * CPU Units: 100
    * Memory
        * Memory (MiB): 4096 (use 8192 if your proxmox server has at least 16 GB of RAM)
        * Minimum memory (MiB): 4096 (use 8192 if your proxmox server has at least 16 GB of RAM)
    * Network
        * Name: eth0
        * MAC address: auto
        * Bridge: vmbr0
        * VLAN Tag: 20
        * Firewall: Checked
        * IPv4: Temporarily set this to a static IP address that is in your OPNSense LAN subnet.
4. Install Kali.
5. Log-in to Kali.
6. Open Terminal.
7. Verify network connectivity with the commands ```ping **IP address of your OPNSense LAN Interface**```, ```ping **IP address of your OPNSense WAN Interface**```, ```ping 8.8.8.8```, and ```dig @8.8.8.8 google.com```. If you cannot ping 8.8.8.8 or resolve google.com, do not continue until you fix the problem.
8. Update Kali software.

    ```bash
    sudo apt get update && sudo apt get upgrade -y
    ```
9. Congratulations, you now have a Kali VM connected to the network through your virtual OPNSense firewall.