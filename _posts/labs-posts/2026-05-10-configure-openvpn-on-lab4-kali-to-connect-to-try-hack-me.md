---
layout: post
title: "Lab 5: Configure OpenVPN on lab4-kali to connect to TryHackMe"
categories: [labs, proxmox]
tags: [windows, proxmox]
after-content: [disclaimer-notice.html]
---
This is lab number 5 in this series. If you have not already completed the previous lab, please start at [Lab 4: Create an Kali VM for TryHackMe](https://4d5a.github.io/2026-05-10-create-a-kali-virtual-machine-for-try-hack-me/).

In this lab, you will use OpenVPN to connect your Kali VM to the TryHackMe VPN server.

Before you start setting up OpenVPN on your Kali VM, let's structure our home directory.

You can find your home directory at "/home/**username**" or "~". Either browse to that location or open Terminal and change directories ```bash cd``` to it.

First, make a new directory named "bin" in your home directory. You can either do this through the Window Manager or via Terminal by entering ```bash mkdir ~/bin```.

Now let's download our OpenVPN configuration file from TryHackMe.

1. Login to TryHackMe.
2. Click your profile picture.
3. Click "Manage Account".
4. Click "VM and VPN Settings".
5. Scroll down to "OpenVPN (Advanced)".
6. Click "Download configuration file".
7. Move the configuration file to ```bash ~/bin```.
8. Open Terminal.
9. Use the following command to connect to the TryHackMe VPN.

```bash
sudo openvpn ~/bin/**OpenVPN configuration file**
```