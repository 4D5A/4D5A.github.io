---
layout: post
title: What are passkeys
gh-repo: 4D5A
gh-badge: [follow]
categories: [blog]
tags: [passkeys, Multi-Factor Authentication, authentication, 2FA, MFA, Two-Step Verification]
after-content: [disclaimer-notice.html]
---
## **Understanding Passkeys: A New Era of Authentication**

In today’s digital world, securing online identities has never been more crucial. As cyber threats evolve, so do the methods for protecting ourselves. Enter **passkeys**, a sophisticated and user-friendly authentication mechanism designed to enhance security and streamline user experience.

### **What Are Passkeys?**

Passkeys are a form of Multi-Factor Authentication (MFA) that replaces traditional passwords with cryptographic key pairs. These key pairs are made up of a public key, which is stored on the server, and a private key, which remains securely on the user’s device, never leaving it. This method is intended to be both secure and convenient, eliminating the need for users to remember complex passwords.

### **How Do Passkeys Work?**

When you register with an online service that supports passkeys, the service will ask your device to create a new key pair. Your device stores the private key securely (typically in a hardware-backed secure enclave) and registers the public key with the online service. To authenticate, the service will challenge your device to prove it has the private key by signing a message. The device then sends back the signed message, which the service verifies using the public key. This process proves your identity without transmitting sensitive information.

### **Required Hardware and Software**

To use passkeys, you need:
- **A compatible device**: Most modern smartphones, tablets, and computers can generate and store passkeys.
- **Operating system support**: Devices must run software that supports passkey standards, such as iOS, Android, Windows, or MacOS.
- **A passkey-enabled service**: The website or app you're accessing must support passkeys as an authentication method.

### **Advantages Over Traditional MFA Methods**

Passkeys offer several advantages over traditional password-based or token-based MFA methods:
- **Enhanced Security**: As passkeys do not leave the device, they are immune to phishing and replay attacks.
- **Convenience**: Users do not need to remember passwords or input codes from an authenticator app.
- **Simplified User Experience**: Authentication can be as simple as unlocking your device using a biometric or a PIN.

### **Services Supporting Passkeys**

Several major services have embraced passkeys, enhancing user security while maintaining ease of use. Some of these include:

1. **Apple iCloud**: [Set up passkeys for Apple iCloud](https://support.apple.com/passkeys)
2. **Google Accounts**: [Configure passkeys for Google](https://support.google.com/accounts/answer/passkeys)
3. **Microsoft Accounts**: [Enable passkeys on Microsoft](https://support.microsoft.com/en-us/account-billing/passkeys)
4. **Facebook**: [Facebook passkey setup](https://www.facebook.com/security/passkeys/setup)

These platforms provide detailed setup guides to help users transition to passkey authentication seamlessly.

### **Passkeys and Phishing Resistant MFA**

According to security frameworks like NIST's Digital Identity Guidelines, passkeys meet the criteria for **phishing-resistant MFA**. This is due to the cryptographic proof involved in the authentication process, which cannot be fooled by forged websites or man-in-the-middle attacks, thus significantly enhancing security.
