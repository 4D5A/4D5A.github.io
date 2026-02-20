---
layout: post
title: How to configure exchange online to block hyperlinks to zip top level domains
gh-repo: 4D5A
gh-badge: [follow]
categories: [blog]
tags: [Email, Microsoft 365, Exchange Online]
after-content: [disclaimer-notice.html]
---

Import the Exchange Online PowerShell Module

```Import-Module ExchangeOnline```

Get the current Acceptable Use Policy (AUP)

```$aup = Get-AUP```

Add a new rule to the AUP to block all hyperlinks that use the top level domain .zip

~~~
$rule = New-AUPRule -Name "Block .zip Links" -Description "Blocks all hyperlinks that use the top level domain .zip" -Action Block -Conditions @{
UrlMatches = { ".*\\.zip$" }
}
~~~

Update the AUP with the new rule

```Set-AUP -Identity $aup -Rules $rule```

Save the AUP

```Set-AUP -Identity $aup -Save```

This script will first import the Exchange Online PowerShell Module. Then, it will get the current AUP. Next, it will add a new rule to the AUP to block all hyperlinks that use the top level domain .zip. Finally, it will update the AUP with the new rule and save it.

Once this script is run, all hyperlinks that use the top level domain .zip will be blocked in Exchange Online. This will help to protect users from malicious websites that use .zip files to distribute malware.