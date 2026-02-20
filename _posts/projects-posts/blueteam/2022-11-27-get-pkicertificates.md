---
layout: post
title: Get-PKICertificates
gh-repo: 4D5A/Systems-Administration
gh-badge: [follow, star]
categories: [blueteam, projects]
tags: [DNS, Systems-Administration-Tools]
after-content: [disclaimer-notice.html]
---
### Introduction
When I first started scripting with PowerShell, I came across the Get-PSDrive cmdlet. [https://learn.microsoft.com/en-us/powershell/module/microsoft.powershell.management/get-psdrive?view=powershell-7.3](https://learn.microsoft.com/en-us/powershell/module/microsoft.powershell.management/get-psdrive?view=powershell-7.3) Immediately one of the things that stood out to me was that with PowerShell, it was possible to access storage devices, the registry, and certificate stores similar to the process of accessing storage devices with Windows Command Prompt. One of the PowerShell scripts I wrote allowed me to delete or add a certificate to the certificate store. Years later, a post by [@mubix@infosec.exchange](https://infosec.exchange/@mubix), helped me think of a possible use case for such a script. According to [@mubix@infosec.exchange](https://infosec.exchange/@mubix),<sup>1</sup> there were pentesters who in some cases need to install certificates during their work but don't remember to delete them later which can create security vulnerabilities. He suggested that Network Defenders require thumbprints for all certificates created by the pentester(s) so the Network Defenders can delete them.

### Accessing Windows' Certificate Stores in PowerShell
Get-PKICertificates gives the option of choosing from the following System and Physical stores:

#### System Stores

* CurrentUser
* LocalMachine

#### Physical Stores

* AddressBook
* AuthRoot
* CertificateAuthority
* Disallowed
* My
* Root
* TrustedPeople
* TrustedPublisher

### How to delete certificates
If you do not specify the SystemStore and PhysicalStore to use, the script will use the default. The default SystemStore is **CurrentUser**. The default PhysicalStore is **My**.

#### How to delete a certificate by its thumbprint
```powershell
Get-PKICertificates -DeleteCertificate XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX
```

#### How to bulk delete certificates with their thumbprints
If there is a need to bulk delete certificates and you know their thumbprints, you can create a CSV file that has a header of *thumbprint*,*systemstore*.*physicalstore*.

If you create the CSV file with those columns and provide the information for each certificate you can run the following command to delete the certificates by their thumbprints.

```powershell
Get-PKICertificates -BulkDelete C:\certificates-to-delete.csv
```

#### How to delete a certificate by its subject
> If you choose to delete a certificate by its subject, you should exercise extreme caution because the command ```Get-PKICertificates -DeleteBySubject www.example.com``` will delete all certificates that are *-like "*$DeleteBySubject*"*. Please read [https://technet.microsoft.com/en-us/library/hh847759.aspx](https://technet.microsoft.com/en-us/library/hh847759.aspx) for additional information on how PowerShell processes information compared with the -like operator before you choose to use DeleteBySubject.

```powershell
Get-PKICertificates -DeleteBySubject www.example.com
```

### Download Get-PKICertificates
Click [Get-PKICertificates](https://github.com/4D5A/Systems-Administration/blob/main/Windows/Configuration%20Management/Get-PKICertificates/Get-PKICertificates.ps1) to download Get-PKICertificates.
