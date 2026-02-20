---
layout: post
title: Invoke-DNSQuery
gh-repo: 4D5A/Networks-Administration
gh-badge: [follow, star]
categories: [blueteam, projects]
tags: [DNS, Network-Administration-Tools]
after-content: [disclaimer-notice.html]
---
### Introduction
Many Network Administrators and Systems Administrators frequently respond to questions about why an email wasn't received by someone in their organization. The questions are often asked with a tone of "why did you block my email?" or "something is obviously wrong, because I should have received that email" and the people asking sometimes come off as accusing the Network Administrator or Systems Administrator of doing something wrong. Many times, emails are blocked by spam filters because they failed one or more processes to verify the email was sent from an authorized server for the sending domain name and the content of the email did not change from the time it was sent until the time it was received. The processes verify that information are named Sender Policy Framework (SPF), DomainKeys Identified Mail (DKIM), and Domain-based Message Authentication Reporting & Conformance (DMARC).    

If SPF and DKIM are not familiar to you, the resources below provide more details for each.

- [Configuring sender policy framework in Exchange Online](https://4d5a.github.io/2022-04-23-configuring-sender-policy-framework-in-exchange-online/)
- [How to configure DKIM in Microsoft 365](https://4d5a.github.io/2022-04-23-how-to-configure-dkim-in-microsoft-365/)

For a technical description of SPF including a discussion of RFC 7208, you may consider reviewing [Blocking spoofed emails](https://4d5a.github.io/2021-12-11-blocking-spoofed-emails/).

### Manually reviewing SPF, DKIM, and DMARC records
In the past, when someone asked Network Administrators and Systems Administrators about an email which was blocked, the Network Administrator or Systems Administrator would review the email filter's logs and often use a website like [mxtoolbox.com](https://www.mxtoolbox.com) to check for problems with the sending domain's SPF and DMARC records. Given the frequency of being asked this type of question, I wanted a PowerShell script that could check the public DNS information of the sending domain to see if there were SPF or DMARC misconfigurations. Specifically, I wanted the script to query public DNS for the sending domain's SPF and DMARC records, identify if there were duplicate records, identify if those records were missing, the SPF qualifier, the DMARC mode, and to attempt to determine the email or filter provider for the queried domain name. I also wanted to be able to query multiple domain names at once and export the results to a csv file.

> What about DKIM?

DKIM uses a public key on a public server to decrypt the DKIM Signature on an email.<sup>1</sup> A DNS record is created that specifies the public key to use to decrypt DKIM Signatures.<sup>2</sup> That DNS record is referred to as a "DKIM Selector".<sup>3</sup> Google refers to DKIM Selectors as "prefix selectors".<sup>4</sup> DKIM Selectors can either be A records or CNAME records.<sup>5</sup> Since third party email service provider's servers store the private key on their server, we normally only need to create a CNAME record that points to the third party email service provider's public key.<sup>6</sup>

For example, if you use DKIM for signing outbound emails from Exchange Online, there are three changes you need to make:

1. Create DKIM Keys<sup>7</sup>
2. Create two CNAME records for your DKIM Selectors<sup>8</sup>
3. Enable DKIM<sup>9</sup>

Another feature making querying DKIM Selectors difficult is that DKIM offers you the ability to have multiple public/private key pairs for one domain name so differnet email services that you use to send email have their own DKIM signatures. This allows you to say, use one public/private keypair to DKIM sign emails sent using your domain name from Exchange Online and another for emails sent using your domain name from a third party (e.g. servers that you use to send sales emails from your domain name and servers you use to send marketing emails from your domain name). Because DKIM Selectors can be CNAME records and you would need to know what the CNAME records are to query them, and one domain name can have multiple public/private keypairs, it is difficult to automate the process of checking for those DKIM records.

#### Using MX records to determine the email or filter provider

Here is a table of strings which may be located in MX records and matching email or filter providers. This information is subject to change and must not be considered to be exhaustive. You should refer to current documentation provided by each email and filter provider for what MX records they each require.

| MX Record | Email / Email Spam Filter Provider |
| olc.protection.outlook.com | Outlook.com |
| mail.protection.outlook.com | Exchange Online |
| mimecast.com | Mimecast |
| pphosted.com | Proofpoint |
| hydra.sophos.com | Sophos |
| barracudanetworks.com | Barracuda Networks |
| secureserver.net | GoDaddy |
| yahoodns.net | Yahoo |
| aspmx.l.google.com | Google |

PowerShell includes the ```Resolve-DnsName``` cmdlet which provides you with the ability to query DNS records. Unlike the output obtanied from ```nslookup```, ```Resolve-DnsName``` produces object-oriented output, which in turn makes assigning a specific portion of a DNS query result as a variable. Microsoft's documentation for the ```Resolve-DnsName``` cmdlet is accessible at [https://learn.microsoft.com/en-us/powershell/module/dnsclient/resolve-dnsname](https://learn.microsoft.com/en-us/powershell/module/dnsclient/resolve-dnsname).<sup>10</sup>

An example of using the ```Resolve-DnsName``` cmdlet might be querying Google's Public DNS server 8.8.8.8 for the MX records of google.com.

```Resolve-DnsName -Name google.com -Type MX -Server 8.8.8.8 -DnssecCd```

The example query returns the below object.

| Name | Type | TTL | Section | NameExchange | Preference |
| google.com | MX | 201 | Answer | smtp.google.com | 10 |

Since the ```Resolve-DnsName``` cmdlet is object-oriented, assigning the output of the query to a variable is staightforward.

```$DNSRecord = Resolve-DnsName -Name google.com -Type MX -Server 8.8.8.8 -DnssecCd```

Now that I assigned a variable to the object, I can use the usual PowerShell commands to include Select-Object and Where-Object to get the specific object properties and assign a variable to them.

```$DNSRecord = Resolve-DnsName -Name google.com -Type MX -Server 8.8.8.8 -DnssecCd | Select-Object NameExchange -ExpandProperty```

If you copy and paste the code above in PowerShell and then type ```$MailExchangeRecord```, you will receive the below output.

```smtp.google.com```

Since most domain names have multiple DNS records for failover, I decided to focus on the primary MX record for the domain name by sorting the output by the object property ```Preference``` in descending order and then using Select-Object to select the last entry which will produce the MX record for the domain name with the object property ```Preference``` that has the lowest value.

```$DNSRecord = Resolve-DnsName -Name google.com -Type MX -Server 8.8.8.8 -DnssecCd | Sort-Object -Property Preference -Descending | Select-Object -Last 1 | Select-Object -ExpandProperty NameExchange```

#### DKIM

Often, vendors suggest using "s1" and "s2" as the DKIM Selectors.<sup>11,</sup><sup>12,</sup><sup>13</sup>

Microsoft notes that Microsoft 365 selectors are "selector1" and "selector2" so we can automate the process of checking domain names which use Microsoft 365 for DKIM records.<sup>14</sup>

Here is a short table that summarizes some vendors and the DKIM Selectors they discuss using in their instructions. The table below contains information which is subject to change. Because the name used as a DKIM Selector can be chosen by the DNS Administrator for a particular domain name and is not restricted to the DKIM Selector names contained in the table below, this information provides us "well-known" information which can be added to the PowerShell script to attempt to locate one or more DKIM records for a domain name.

| Vendor | DKIM Selectors | Instructions
| SendGrid | s1 and s2 | [https://docs.sendgrid.com/ui/account-and-settings/dkim-records](https://docs.sendgrid.com/ui/account-and-settings/dkim-records) |
| PowerDMARC | s _n_ (e.g. s1, s2, s3, etc.) | [https://powerdmarc.com/multiple-dkim-records-on-email-domain/](https://powerdmarc.com/multiple-dkim-records-on-email-domain/) |
| EasyDMARC | s1 | [https://easydmarc.com/tools/dkim-record-generator/](https://easydmarc.com/tools/dkim-record-generator/) |
| DMARCLY | s1 | [https://dmarcly.com/blog/how-to-add-dkim-record-in-godaddy-godaddy-dkim-setup-guide](https://dmarcly.com/blog/how-to-add-dkim-record-in-godaddy-godaddy-dkim-setup-guide)
| Plesk | default | [https://docs.plesk.com/en-US/obsidian/customer-guide/mail-settings/enabling-dkim-email-signing.74718/](https://docs.plesk.com/en-US/obsidian/customer-guide/mail-settings/enabling-dkim-email-signing.74718/) |

In Invoke-DNSQuery, an array named "$DKIMPrefixes" is created and "s1" and "s2" are members of the array initially.

A query is made for a TXT record with a value that includes the string "MS=" (This string is used by Microsoft in the domain name verification process. Microsoft's domain name verification process must be complete before a custom domain name can be used in Microsoft 365.)<sup>15</sup> There are two important notes about Microsoft's domain name verification process.

1. After the domain name is verified, the TXT record can be removed.
2. Microsoft offers verifying the domain name by uploading a txt file to the root of your domain name's website<sup>16</sup> or by adding an MX record <sup>17</sup>.

If a TXT record with a value that includes the string "MS=" is found, the DKIM Prefixes "selector1" and "selector2" are added to the $DKIMPrefixes array.

After the $DKIMPrefixes array is complete, the script uses the following command to resolve the DKIM Record Names for each DKIM Prefix in the $DKIMPrefixes array.

```(Resolve-DnsName -Name "$DKIMPrefix._domainkey.$DomainName" -Type TXT -Server $DnsIp -DnssecCd).NameHost```

The results are added an array named $DKIMRecordNames.

The script calculates the number of identified DKIM Records and if the number is greater than 0, the script runs the command below to obtain the DKIM signatures of each DKIM Record located for the domain name.

```(Resolve-DnsName -Name "$DKIMRecordName" -Type TXT -Server $DnsIp -DnssecCd).Strings```

The DKIM Records and DKIM Signatures are added as properties to a custom object.

#### Using TXT records to detect missing or misconfigured SPF, DKIM, and DMARC records
There are common misconfigurations of SPF, DKIM, and DMARC records which we can use to TXT records to test for.

##### Using TXT records to detect missing or misconfigured SPF records
Two common SPF record problems are there not being one or there being more than one. If the domain name does not have an SPF record, destination spam filters may block emails from that domain name. If the domain name has multiple SPF records, when the destination spam filter receives the email from that domain name, the destination spam filter will request the SPF record for the domain name and the DNS server will provide the value of one of the SPF records. If those records are different (and one of the records is wrong), some emails from the sending domain will pass the SPF check of the spam filter and others will fail. This presents itself as emails from a given domain name sporadically being blocked.

We are able to use ```Resolve-DNSName``` to obtain SPF records for a domain name.

```$SPFDetails = Resolve-DnsName -Name $DomainName -Type TXT -Server $DnsIp -DnssecCd | Where-Object -Property Strings -match -Value "spf1"```

I could use an ```If``` statement like ```If (-Not($SPFDetails))``` with the example variable above to check if a domain name does not have an SPF record, but because I also plan on checking if there are multiple SPF records, I decided to count the number of SPF records.

```$SPFRecordCount = (Resolve-DnsName -Name $DomainName -Type TXT -Server $DnsIp -DnssecCd | Where-Object -Property Strings -match -Value "spf1").count```

##### Using TXT records to check for missing or misconfigured DMARC records

A similiar command can be used to check for missing of misconfigured DMARC records. A domain name should have one DMARC record. Invoke-DNSQuery counts the number of DMARC records, and the number is zero, the domain name is missing a DMARC record. If Invoke-DNSQuery counts the number of DMARC records, and the number is greater than one, there is a DMARC related misconfiguration.

```$DMARCRecordCount = (Resolve-DnsName -Name _dmarc.$DomainName -Type TXT -Server $DnsIp -DnssecCd | Where-Object -Property Name -match -Value "_dmarc.$DomainName").count```

##### Using CNAME records to check for missing or misconfigured DKIM records for domain names which use Exchange Online for email

Since there isn't a way to identify all of the DKIM records for a domain name with any certainty, Invoke-DNSQuery isn't able to count the number of DKIM records the same way it counted SPF and DMARC records. Instead, Invoke-DNSQuery looks for DKIM records with a handful of well-known DKIM Prefixes. Detected DKIM records are stored in the array named, $DKIMRecordNames and counted.

If the array $DKIMRecordName does not contain any DKIM records, the domain name _may_ be missing a DKIM record.

~~~
$DKIMPrefixes = "s1","s2"
    If ($MicrosoftTextDNSRecord) {
        $DKIMPrefixes += "selector1","selector2"
    }
    Foreach ($DKIMPrefix in $DKIMPrefixes) {
        $DKIMRecordName = (Resolve-DnsName -Name "$DKIMPrefix._domainkey.$DomainName" -Type TXT -Server $DnsIp -DnssecCd).NameHost
        If ($DKIMRecordName) {
            $DKIMRecordNames += $DKIMRecordName
        }
    }
    $DKIMRecordCount = ($DKIMRecordNames).Count
    If ($DKIMRecordCount -eq 0) {
        $DKIMRecordNames = "None"
        $DKIMRecords = "MISCONFIGURATION: No DKIM record."
    }
~~~

### Invoke-DNSQuery
This script is written in PowerShell. It does not require additional modules. It does not require administrator rights.

This script has one required parameter.

1. ```$DomainNames``` - ```$DomainNames``` accepts multiple values as a comma-seperated list. 

The script has three optional parameters.

1. ```$ReportLocation``` - If this optional parameter is not specified, the value for ```$ReportLocation``` will be set to ```"$env:USERPROFILE\Desktop\"```.

2. ```$File``` - If this optional paramter is not specified, the value for ```$File``` will be set to ```"DNS_Report-$DomainNames-$(Get-Date -Format ddMMyyyy_HHmmss).csv"```.

3. ```$DnsIp``` - If this optional parameter is not specified, the value for ```$DnsIp``` will be set to ```"8.8.8.8"```.

This script has two optional switches.

1. ```$Csv``` - If this optional switch is specified, results are sent to the console and a csv file.

2. ```$details``` -  If this parameter is not specified, the object's DomainName, MailExchangerDNSRecord, EmailFilter, SPFMode, and DMARCMode properties are displayed in the console. If this parameter is specified, all of the object's properties are displayed in the console. If the Csv parameter is specified, all of the object's properties are sent to the csv file.

### Examples

```Invoke-DNSQuery.ps1 -DomainNames example.org```

```Invoke-DNSQuery.ps1 -DomainNames example.org, example.com```

```Invoke-DNSQuery.ps1 -DomainNames example.org -Csv -ReportLocation C:\ -File invoke-dnsquery-results.csv```

```Invoke-DNSQuery.ps1 -DomainNames example.org -details```

```Invoke-DNSQuery.ps1 -DomainNames example.org -Csv -ReportLocation C:\ -File invoke-dnsquery-results.csv -details```

### Download Invoke-DNSQuery

Click [Invoke-DNSQuery](https://github.com/4D5A/Networks-Administration/blob/main/Invoke-DNSQuery/Invoke-DNSQuery.ps1) to download Invoke-DNSQuery.

### Footnotes
[1] [https://support.google.com/a/answer/11611356?hl=en](https://support.google.com/a/answer/11611356?hl=en)

[2] [https://support.google.com/a/answer/11611356?hl=en](https://support.google.com/a/answer/11611356?hl=en)

[3] [https://support.google.com/a/answer/11611356?hl=en](https://support.google.com/a/answer/11611356?hl=en)

[4] [https://support.google.com/a/answer/11611356?hl=en](https://support.google.com/a/answer/11611356?hl=en)

[5] [https://easydmarc.com/tools/dkim-record-generator/](https://easydmarc.com/tools/dkim-record-generator/)

[6] [https://learn.microsoft.com/en-us/microsoft-365/security/office-365-security/email-authentication-dkim-configure?view=o365-worldwide#steps-to-create-enable-and-disable-dkim-from-microsoft-365-defender-portal](https://learn.microsoft.com/en-us/microsoft-365/security/office-365-security/email-authentication-dkim-configure?view=o365-worldwide#steps-to-create-enable-and-disable-dkim-from-microsoft-365-defender-portal)

[7] [https://learn.microsoft.com/en-us/microsoft-365/security/office-365-security/email-authentication-dkim-configure?view=o365-worldwide#steps-to-create-enable-and-disable-dkim-from-microsoft-365-defender-portal](https://learn.microsoft.com/en-us/microsoft-365/security/office-365-security/email-authentication-dkim-configure?view=o365-worldwide#steps-to-create-enable-and-disable-dkim-from-microsoft-365-defender-portal)

[8] [https://learn.microsoft.com/en-us/microsoft-365/security/office-365-security/email-authentication-dkim-configure?view=o365-worldwide#publish-two-cname-records-for-your-custom-domain-in-dns](https://learn.microsoft.com/en-us/microsoft-365/security/office-365-security/email-authentication-dkim-configure?view=o365-worldwide#publish-two-cname-records-for-your-custom-domain-in-dns)

[9] [https://learn.microsoft.com/en-us/microsoft-365/security/office-365-security/email-authentication-dkim-configure?view=o365-worldwide#steps-to-create-enable-and-disable-dkim-from-microsoft-365-defender-portal](https://learn.microsoft.com/en-us/microsoft-365/security/office-365-security/email-authentication-dkim-configure?view=o365-worldwide#steps-to-create-enable-and-disable-dkim-from-microsoft-365-defender-portal)

[10] [https://learn.microsoft.com/en-us/powershell/module/dnsclient/resolve-dnsname](https://learn.microsoft.com/en-us/powershell/module/dnsclient/resolve-dnsname)

[11] [https://easydmarc.com/tools/dkim-record-generator/](https://easydmarc.com/tools/dkim-record-generator/)

[12] [https://docs.sendgrid.com/ui/account-and-settings/dkim-records](https://docs.sendgrid.com/ui/account-and-settings/dkim-records)

[13] [https://powerdmarc.com/multiple-dkim-records-on-email-domain/](https://powerdmarc.com/multiple-dkim-records-on-email-domain/)

[14] [https://learn.microsoft.com/en-us/microsoft-365/security/office-365-security/email-authentication-dkim-configure?view=o365-worldwide#publish-two-cname-records-for-your-custom-domain-in-dns](https://learn.microsoft.com/en-us/microsoft-365/security/office-365-security/email-authentication-dkim-configure?view=o365-worldwide#publish-two-cname-records-for-your-custom-domain-in-dns)

[15] [https://learn.microsoft.com/en-us/microsoft-365/admin/get-help-with-domains/create-dns-records-at-any-dns-hosting-provider?view=o365-worldwide](https://learn.microsoft.com/en-us/microsoft-365/admin/get-help-with-domains/create-dns-records-at-any-dns-hosting-provider?view=o365-worldwide)

[16] [https://learn.microsoft.com/en-us/microsoft-365/admin/setup/add-domain?view=o365-worldwide](https://learn.microsoft.com/en-us/microsoft-365/admin/setup/add-domain?view=o365-worldwide)

[17] [https://learn.microsoft.com/en-us/microsoft-365/admin/get-help-with-domains/create-dns-records-at-any-dns-hosting-provider?view=o365-worldwide](https://learn.microsoft.com/en-us/microsoft-365/admin/get-help-with-domains/create-dns-records-at-any-dns-hosting-provider?view=o365-worldwide)
