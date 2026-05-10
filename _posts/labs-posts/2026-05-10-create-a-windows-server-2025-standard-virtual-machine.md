---
layout: post
title: "Lab 6: Create a Windows Server 2025 Standard Virtual Machine"
categories: [labs, proxmox]
tags: [windows, proxmox]
after-content: [disclaimer-notice.html]
---
This is lab number 6 in this series. If you have not already completed the previous lab, please start at [Lab 5: Configure OpenVPN on lab4-kali to connect to TryHackMe](https://4d5a.github.io/2026-05-10-configure-openvpn-on-lab4-kali-to-connect-to-try-hack-me/).

1. Visit [https://www.microsoft.com/en-us/evalcenter/download-windows-server-2025](https://www.microsoft.com/en-us/evalcenter/download-windows-server-2025) and download the Windows Server 2025 64-bit edition ISO.
2. Upload the ISO to "ISO Images" on your proxmox server.
3. Create a proxmox VM using the following settings.
    * General
        * Node: pve
        * VM ID: 202
        * Name: lab6-DC1
        * Start at boot: Checked
    * OS
        * Use CD/DVD disc image file (iso): Yes
            * Storage: Pick the storage path where "ISO Image" are stored.
            * ISO Image: 26100.32230.260111-0550.lt_release_svc_refresh_SERVER_EVAL_x64FRE_en-us.iso
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
        * Bridge: vmbr0
        * VLAN Tag: 20
        * Model: VirtIO (paravirtualized)
        * Firewall: Checked
20. Click "Confirm".
21. Click "Finish".
22. Expand your node.
23. Click your "lab6-DC1" VM.
24. Click "Hardware".
25. Click "Add".
26. Click "TPM State".
27. To the right of "TPM Storage:" choose where to store the vTPM.
28. Click "Add".
29. Click "Options".
30. Click "Boot Order".
31. Click "Edit".
32. Find your virtual DVD drive and move it to the top of the Boot Order.
33. Click "OK".
34. Click "Console".
35. Click "Start".
36. Boot to the Windows Server 2025 ISO.
37. Install Windows Server 2025.
38. When the installation asks you to choose what version of Windows Server 2025 to install, click "Windows Server 2025 Standard (Desktop)".
39. Click "Next".
40. Login to your your VM with ```Administrator``` for the username and the password you set during the installation process.
41. Check for updates.
42. In Windows Server 2025, rename the server to "lab6-DC1".
43. Restart your server.
44. Assign a static IP address to the server's network adapter.
    * You can do this in Windows using this process.
        * Open "Control Panel"
        * Click "Network and Sharing Center"
        * Click "Change adapter settings"
        * Right click the virtual network adapter.
        * Click "Properties".
        * Click "Internet Protocol Version 4 (TCP/IPv4").
        * Click "Properties".
        * Click "Use the following IP address:".
        * Set your static IP address, subnet mask, and default gateway.
        * Click "Use the following DNS server addressses:".
        * Set your Preferred DNS server to the static IP address you set and Alternate DNS server to 127.0.0.1.
45. Open Server Manager
46. Select and install the "Active Directory Domain Services" and "DNS Server" Roles. 

    > Installing Active Directory Domain Services can be confusing if you have never done that before. If this is the first Domain Controller in the domain, you need to choose to create a new domain. For the domain name, best practice is to use a subdomain instead of the root. As an example, if your domain name is acme2.com, you might use the subdomain ad.acme2.com as your Windows domain name.

47. Restart your Windows Server 2025 Standard server.

    * Domain joined machines need to use DNS servers that are domain joined including DCs. But how does a DC know how to resolve DNS queries? They use DNS Forwarders. Domain joined machines query domain joined DNS servers first. This allows domain joined DNS servers to respond with data related to the Windows domain. Requests for DNS zones not managed by the domain joined DNS server are forwarded to the servers specified as DNS Forwarders on domain joined DNS servers.

48. Open DNS Server management.
49. Add DNS Forwarders.
    * Use 8.8.8.8 and 8.8.4.4 as DNS Forwarders.
50. Congratulations, you now have a Windows Server 2025 VM that is functioning as a Domain Controller. This allows you to use Active Directory and Group Policy Management to manage accounts and policies on domain joined devices.

51. Bonus task - Enable BitLocker encryption on lab6-DC1's system drive.
52. Second bonus task - There is a way to configure the domain so domain joined computers automatically backup their BitLocker Recovery Key to Active Directory.

    >Hint: This requires installing a Windows Server feature, creating a Group Policy Object (technically, you could add the configurations to the "Default Domain Policy" but it is best practice to reserve the "Default Domain Policy" and "Default Domain Controllers" policy to a handful of system configurations), and linking it to the correct Active Directory Organizational Unit.

    >>Riddle: The difference between these two AD object types, both of which may contain user or computer objects, determines whether or not a Group Policy Object can be linked to it.

In this configuration, you will need your OPNSense firewall running for your Windows Server 2025 VM to have network connectivity.

Here is a list of TryHackMe rooms that introduce you to Windows and Windows AD.

[https://tryhackme.com/room/windowsfundamentals1xbx](https://tryhackme.com/room/windowsfundamentals1xbx)
[https://tryhackme.com/room/windowsfundamentals2x0x](https://tryhackme.com/room/windowsfundamentals2x0x)
[https://tryhackme.com/room/windowsfundamentals3xzx](https://tryhackme.com/room/windowsfundamentals3xzx)
[https://tryhackme.com/room/winadbasics](https://tryhackme.com/room/winadbasics)
[https://tryhackme.com/room/windowscommandline](https://tryhackme.com/room/windowscommandline)
[https://tryhackme.com/room/windowspowershell](https://tryhackme.com/room/windowspowershell)
[https://tryhackme.com/room/logsfundamentals](https://tryhackme.com/room/logsfundamentals)
