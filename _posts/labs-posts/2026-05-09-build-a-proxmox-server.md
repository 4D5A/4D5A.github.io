---
layout: post
title: "Lab 1: Create a proxmox server"
categories: [labs, proxmox]
tags: [linux, proxmox]
after-content: [disclaimer-notice.html]
---
This is lab number 1 in this series.

Proxmox is a type 1 hypervisor meaning the proxmox OS that runs on a linux kernel is installed on bare metal hardware. A hypervisor allows you to run virtualized computers. If you have ever wanted to setup multiple computers but didn't have enough desktops for that, virtualization is what you want. Using a hypervisor, you can install one virtual machine running Windows 11, another running Windows Server 2025 Standard, and another Ubuntu. While how many virtual machines you can run on your hypervisor and how much virtual drive space, virtual CPU, and virtual RAM you can allocate to them is limited by your hypervisor's hardware, you can easily run proxmox and a few Linux Containers (LXCs) on a micro form factor PC with an i5 processor and 4 GB of RAM if that is what you currently have.

Linux Containers or LXCs are another virtualization option. LXCs are typically used to virtualize one or more applications without needing to install a dedicated operating system. Since proxmox is built on the linux kernel, you can create LXCs that will run linux avoiding the need for you to create a virtual machine and install a full linux operating system inside of it. You can install applications in your LXCs and set each LXC to emulate a different linux distribution through the use of proxmox templates, while keeping each LXCs "OS" and application configurations seperate. 

What you need:

* A management system (this is what you will use to create your proxmox bootable USB)
* Unused computer (this will become the proxmox server)

Minimum proxmox server requirements for the labs:
* Intel 64 or AMD64 with Intel VT/AMD-V CPU flag.
* At least 8 GB of RAM
* At least 256 GB of storage space
* Network adapter (wired or wireless)

Recommended proxmox server requirements for the labs:
* Intel 64 or AMD64 with Intel VT/AMD-V CPU flag.
* 16 GB to 32 GB of RAM
* 500 GB to 1 TB of storage space
* Two network adapters (wired)

**The official Proxmox minimum system requirements are available at [https://www.proxmox.com/en/products/proxmox-virtual-environment/requirements](https://www.proxmox.com/en/products/proxmox-virtual-environment/requirements).**

> Keep in mind, those are the minimum requirements for this lab. This does not include redundant storage devices, redundant network adapters, and does not address important considerations such as RAID / ZFS / Ceph, network segmentation (highly recommended for security), backups, proxmox clusters, or directory services.

1. Download the [Proxmox Virtual Environment ISO](https://www.proxmox.com/en/downloads/proxmox-virtual-environment)

2. If your management system runs Windows, use a tool like [Rufus](https://rufus.ie/) and the ISO to create a bootable USB drive. If your management system runs Mac or linux or if you have problems with Rufus, you can try [Balena Etcher](https://etcher.balena.io/).

3. Boot your unused computer to your USB and install proxmox.

4. When you install proxmox, it will attempt to obtain an IP address from your DHCP server. If you want to specify a static IP address, you can. Make sure that if you specify a static IP address that you pick one that is on your local area network, that isn't currently used, and that is outside of your DHCP scope. Alternatively, you can create a DHCP reservation for your proxmox server which may be simpler and avoids needing to choose a static IP address.

5. After proxmox is installed, go back to your management system and browse to your proxmox server. The web address will be https://Proxmox-Server-IP-Address:8006. The username is "root" and the password will be what you set during the install.

6. Congratulations, you now have a Proxmox server! Before you create virtual machines, here are some additional things to plan for:

* Virtual Machine vs Linux Container (LXC)
* OS Install Media for virtual machines
* Templates for LXCs
* LVM vs LVM-Thin (be careful not to over provision thin-provisioned storage or you may run out of storage space on your server)
* Network segmentation
    * Will the VMs and LXCs connect to your network using the default Proxmox network adapter? By default Proxmox creates a bridged network adapter and all VMs and LXCs connect to your network as if they had their own network connection. They will reach receive their own IP address (from DHCP or by statically assigning them).
    * If you choose to add VLANs, do you want the VLANs to exist only on your proxmox server (this may be useful for small lab environments where you want keep all of your proxmox VLANs on the proxmox server instead of exposing them to the rest of the network) or do you want to connect your proxmox server to a managed switch and add VLANs to the managed switch (if so, you will need to tag each VLAN that you want to be able to assign to your VMs and LXCs to the switch port that your proxmox server is connected to).

If you are new to systems administration or networking and you are looking for a good beginner's training program, the [Google IT Support Professional Certificate](https://www.coursera.org/professional-certificates/google-it-support) program is a good option, presents the information in easy to understand ways, and gives you basic skills needed to start working in these fields.