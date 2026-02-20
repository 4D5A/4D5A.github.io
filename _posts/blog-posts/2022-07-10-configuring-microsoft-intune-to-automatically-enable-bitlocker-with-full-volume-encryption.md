---
layout: post
title: Configuring Microsoft Intune to automatically enable BitLocker with full volume encryption
gh-repo: 4D5A
gh-badge: [follow]
categories: [blog]
tags: [Microsoft 365, Intune, BitLocker]
after-content: [disclaimer-notice.html]
---
There is a great article at [TechNuggets](https://mrshannon.wordpress.com/2020/06/25/enable-bitlocker-silently-using-autopilot-and-intune/) that explains how to Enable BitLocker Silently using Autopilot and Intune. One capability I was hoping existed was to not just enable Intune to Automatically enable BitLocker but to enable it using Full Volume Encryption instead of the default configuration of encrypt used space only.

There are good arguments for why you do not need to configure BitLocker to encrypt a drive using Full Volume Encryption. Two of those arguments are provided below.

1. If the drive has not been used before, if you enable BitLocker with its default configuration of encrypt used space only at the beginning, then the process takes less time and all of the data saved to the drive after BitLocker is enabled will be encrypted.

2. If the drive has been used before, if you enable BitLocker with its default configuration of encrypt used space only after data has been stored on the drive that needs protected, you can run the command manage-bde -wipefreespace which “provides the same level of protection as the **Full Volume Encryption** encryption method.”<sup>1</sup>

The problem is your organization may require BitLocker to be setup with the configuration of Full Volume Encryption for compliance purposes.

As of June 2022, Microsoft Intune now allows you to create a BitLocker Profile configuration which specifies BitLocker will use full disk encryption to encrypt data on OS and fixed data drives.

<img src="{{ 'assets/img/2022-07-10-configuring-microsoft-intune-to-automatically-enable-bitlocker-with-full-volume-encryption/microsoft-intune-bitlocker-base-settings.png' | relative_url }}" alt="Microsoft Intune BitLocker - Base Settings" />

This setting is available in Microsoft Endpoint Manager, Endpoint security, Disk encryption. When creating a Disk encryption policy, the “Enable full disk encryption for OS and fixed data drives” is the first configuration setting under “BitLocker - Base Settings.”

An important thing to confirm if you are testing enabling BitLocker to silently encrypt the OS drive with full drive encryption, is to verify that the CD/DVD drive is empty when you restart the computer. If you are testing this on a virtual machine and there is a virtual CD/DVD (.iso) attached to it when you restart it after applying the Intune Policy to the device, BitLocker Silent Encryption will fail. If having a virtual CD/DVD (.iso) attached to your virtual machine when you restart it is what causes the BitLocker Silent Encryption to fail, you can look for Event ID 853 in Applications and Services Logs, Microsoft, Windows, BitLocker-API, Management log. If you see an event like this, then go to your virtual machine, virtual CD/DVD (.iso) from it, and restart it.

<img src="{{ 'assets/img/2022-07-10-configuring-microsoft-intune-to-automatically-enable-bitlocker-with-full-volume-encryption/bitlocker-api-management-event-853.png' | relative_url }}" alt="BitLocker-API Event 853" />

[1] [https://docs.microsoft.com/en-us/windows-server/administration/windows-commands/manage-bde-wipefreespace](https://docs.microsoft.com/en-us/windows-server/administration/windows-commands/manage-bde-wipefreespace)