---
layout: post
title: How to configure DKIM in Microsoft 365
gh-repo: 4D5A
gh-badge: [follow]
categories: [blog]
tags: [Email, Microsoft 365, Exchange Online, DKIM, DNS, Email Security]
after-content: [disclaimer-notice.html]
---
DomainKeys Identified Mail (DKIM) is a solution for determining if the content of an email was modified after it was sent and to verify that the domain provided in the From address matches the domain name in cryptographic signature added by DKIM.<sup>1</sup>

Microsoft 365 enables DKIM by default for your default singing domain (e.g. example.onmicrosoft.com) but you should also enable it for each of your custom domain names.<sup>2</sup>

To enable DKIM for each of your custom domain names in Microsoft 365, you first need to create two DNS CNAME records for each custom domain at your DNS hosting provider. To illustrate the DNS CNAME records you will need to create, I will use example.com as the domain name. DKIM refers to its records as “DKIM selectors” and normally, you will need to create two DKIM selectors, selector1 and selector2.<sup>3</sup>

Here is a DNS CNAME record for the custom domain name example.com that has the Microsoft initial domain name example.onmicrosoft.com.<sup>4</sup>

~~~
Host name: selector1._domainkey
Points to: selector1-example-com._domainkey.example.onmicrosoft.com
Host name: selector2._domainkey
Points to: selector2-example-com._domainkey.example.onmicrosoft.com
~~~

After both DKIM selectors are created, you will need to login to Microsoft 365 Defender, Click Email & Collaboration, Click Policies & Rules, Click Threat policies, Locate the Rules section, and Click DKIM (or you can click [https://security.microsoft.com/dkimv2](https://security.microsoft.com/dkimv2)).<sup>5</sup>

Next, you will click the custom domain name you created the DNS CNAME records for and slide the Sign messages for this domain with DKIM signatures toggle button to Enabled.<sup>6</sup>

<img src="{{ 'assets/img/2022-04-23-how-to-configure-dkim-in-microsoft-365/sign-messages-for-this-domain-with-dkim-signatures-disabled.png' | relative_url }}" alt="Microsoft 365 Defender Sign messages for this domain with DKIM signatures: Disabled" />

<img src="{{ 'assets/img/2022-04-23-how-to-configure-dkim-in-microsoft-365/cname-record-does-not-exist-for-this-config.png' | relative_url }}" alt="Microsoft 365 Defender Enable Sign messages for this domain with DKIM signatures error" />

If you receive the Client Error above, you should check your DNS CNAME records and make sure they are right. Microsoft provides details instructions on how to enable DKIM for a custom domain name at [https://docs.microsoft.com/en-us/microsoft-365/security/office-365-security/use-dkim-to-validate-outbound-email?view=o365-worldwide](https://docs.microsoft.com/en-us/microsoft-365/security/office-365-security/use-dkim-to-validate-outbound-email?view=o365-worldwide). If your DNS CNAME records are correct, you may need to wait as long as 4 days after the DNS CNAME records are created before Microsoft 365 Defender can detect them.<sup>7</sup>

When the DNS CNAME records are created correctly and Microsoft 365 Defender can detect them, you will be able to slide the Sign messages for this domain with DKIM signatures toggle button to Enabled. After Sign messages for this domain with DKIM signatures is enabled, you will see the toggle button show it is enabled. The screen should look like this:

<img src="{{ 'assets/img/2022-04-23-how-to-configure-dkim-in-microsoft-365/sign-messages-for-this-domain-with-dkim-signatures.png' | relative_url }}" alt="Microsoft 365 Defender Sign messages for this domain with DKIM signatures: Enabled" />

With DKIM enabled for outbound emails sent from your custom domain now, when you send emails the headers will be encrypted using a unique 1024 bit or 2048 bit RSA private key hosted by Microsoft.<sup>8</sup> The encrypted headers are added to the email as a signature. If your recipient’s email server is configured to verify the DKIM signatures of emails it receives, then it will request the DKIM public key that the DNS CNAME records for the sending domain name point to and decrypt the DKIM signature. If the email headers match the data from the decrypted DKIM signature, then the email will pass DKIM verification.<sup>9</sup>

After you DKIM and SPF are configured for your domain name, you should consider configuring Domain-based Message Authentication, Reporting & Conformance (DMARC).

[1] [https://docs.microsoft.com/en-us/powershell/module/exchange/get-dkimsigningconfig](https://docs.microsoft.com/en-us/powershell/module/exchange/get-dkimsigningconfig)

[2] [https://docs.microsoft.com/en-us/microsoft-365/security/office-365-security/use-dkim-to-validate-outbound-email?view=o365-worldwide#steps-to-manually-upgrade-your-1024-bit-keys-to-2048-bit-dkim-encryption-keys](https://docs.microsoft.com/en-us/microsoft-365/security/office-365-security/use-dkim-to-validate-outbound-email?view=o365-worldwide#steps-to-manually-upgrade-your-1024-bit-keys-to-2048-bit-dkim-encryption-keys)

[3] [https://docs.microsoft.com/en-us/microsoft-365/security/office-365-security/use-dkim-to-validate-outbound-email?view=o365-worldwide#steps-to-manually-set-up-dkim](https://docs.microsoft.com/en-us/microsoft-365/security/office-365-security/use-dkim-to-validate-outbound-email?view=o365-worldwide#steps-to-manually-set-up-dkim)

[4] [https://docs.microsoft.com/en-us/microsoft-365/security/office-365-security/use-dkim-to-validate-outbound-email?view=o365-worldwide#steps-to-manually-set-up-dkim](https://docs.microsoft.com/en-us/microsoft-365/security/office-365-security/use-dkim-to-validate-outbound-email?view=o365-worldwide#steps-to-manually-set-up-dkim)

[5] [https://docs.microsoft.com/en-us/microsoft-365/security/office-365-security/use-dkim-to-validate-outbound-email?view=o365-worldwide#to-enable-dkim-signing-for-your-custom-domain-in-the-microsoft-365-defender-portal](https://docs.microsoft.com/en-us/microsoft-365/security/office-365-security/use-dkim-to-validate-outbound-email?view=o365-worldwide#to-enable-dkim-signing-for-your-custom-domain-in-the-microsoft-365-defender-portal)

[6] [https://docs.microsoft.com/en-us/microsoft-365/security/office-365-security/use-dkim-to-validate-outbound-email?view=o365-worldwide#to-enable-dkim-signing-for-your-custom-domain-in-the-microsoft-365-defender-portal](https://docs.microsoft.com/en-us/microsoft-365/security/office-365-security/use-dkim-to-validate-outbound-email?view=o365-worldwide#to-enable-dkim-signing-for-your-custom-domain-in-the-microsoft-365-defender-portal)

[7] [https://docs.microsoft.com/en-us/microsoft-365/security/office-365-security/use-dkim-to-validate-outbound-email?view=o365-worldwide#to-confirm-dkim-signing-is-configured-properly-for-microsoft-365](https://docs.microsoft.com/en-us/microsoft-365/security/office-365-security/use-dkim-to-validate-outbound-email?view=o365-worldwide#to-confirm-dkim-signing-is-configured-properly-for-microsoft-365)

[8] [https://docs.microsoft.com/en-us/powershell/module/exchange/new-dkimsigningconfig?view=exchange-ps#parameters](https://docs.microsoft.com/en-us/powershell/module/exchange/new-dkimsigningconfig?view=exchange-ps#parameters)

[9] [https://docs.microsoft.com/en-us/microsoft-365/security/office-365-security/use-dkim-to-validate-outbound-email](https://docs.microsoft.com/en-us/microsoft-365/security/office-365-security/use-dkim-to-validate-outbound-email)