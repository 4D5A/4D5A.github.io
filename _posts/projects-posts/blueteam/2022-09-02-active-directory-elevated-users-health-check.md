---
layout: post
title: Active Directory Elevated Users Health Check
gh-repo: 4D5A/Active-Directory-Tools
gh-badge: [follow, star]
categories: [blueteam, projects]
tags: [Active Directory, Active-Directory-Tools]
after-content: [disclaimer-notice.html]
---
### Introduction
I reviewed the configuration of my domain controller and as part of my review, I used a security audit program to review the security configuration of my domain. It found several users which it stated had elevated privileges. I reviewed their Active Directory group membership but they only appeared to be members of Domain Users. I was confused how they could have elevated privileges if they were only members of the Domain Users security group. One possible answer is if Domain Users was added as a member of a privileged group. I checked but Domain Users was not a member of another group. It seemed that the users did not have elevated privileges. That left me with the question, "Why did the tool believe the users had elevated privileges?"

### How does the Active Directory Object Attribute adminCount Work?
After discussing the report with others, and I questioned if the tool was identifying which accounts had elevated privileges by looking at the value of the adminCount attribute on Active Directory User Objects.

When an Active Directory Object is added to Protected Active Directory group<sup>1</sup> (these are different from the Protected Users Security Group), the Security Descriptor Propogator (SDProp), evalutes a number of pieces of metadata associated with the Active Directory object that was added to a Protected Active Directory group. One of its tasks is ensure that the value of the object's adminCount attribute is updated from ```<NOT SET>``` or ```0``` to ```1```.<sup>2</sup> A value of ```1``` for the adminCount attribute, indicates the Active Directory Object currently or previously had elevated privileges as a reulst of being a member of a protected Active Directory group.

> If you remove an Active Directory object from all protected Active Directory groups, SDProp does not reset the value of the adminCount Active Directory attribute to ```0``` or ```<NOT SET>```.<sup>3</sup>

This can lead to reports created by automated security tools identifying Active Directory objects as having elevated privileges when they actually do not. If an Active Directory object was previously added to the "Domain Admins" security group, but was later removed from that security group, it would still have an adminCount attribute value of ```1``` because SDProp does not reset the value of the adminCount attribute when an Active Directory object is removed from a protected group.<sup>4</sup>

### What is the Protected Users group?
If an Active Directory user object is an elevated user and that is what you need it to be, you should consider adding it to the Protected Users group if it isn't. Users that are a member of the Protected Users group receive additional security protections including:

* Credential delegation does not cache the user's credentials in plain text
* Windows Digest does not cache the user's credentials in plain text
* NTLM does not cache the user's credentials in plain text
* Kerberos will not create DES or RC4 keys or cache the user's credentials in plain text

The result is users that are a member of the Protected Users group cannot do the following:

* Authenticate using NTLM
* Authenticate using DES or RC4 through Kerberos
* Renew Kerberos Ticket Granting Tickets for more than the original 4 hours

> Do not enable the Protected Users security group if you have any Windows Server 2003 Domain Controllers in your domain<sup>5</sup>

> To use the Protected Users security group, you need to have your Primary Domain Controller Emulator Role running on a server that is Windows Server 2012 R2 or higher<sup>6</sup>

> Microsoft provides several warnings about adding an Active Directory object to the Protected Users security group. For example, Active Directory accounts for services and computers should not be added to the Protected Users security group. There are other considerations that may apply to your environment. Please refer to [Credentials Protection and Management](https://docs.microsoft.com/en-us/windows-server/security/credentials-protection-and-management/credentials-protection-and-management) and [Protected Users Security Group](https://docs.microsoft.com/en-us/windows-server/security/credentials-protection-and-management/protected-users-security-group) for important information on how the Protected Users security group works, what it will change, when you should not add an Active Directory object to it, and other information.

### Building the Get-ElevatedADUsers Project

#### Project Objectives
My objectives for this project were:

* Identify the Active Directory user objects that have an adminCount attribute other than ```0``` or ```<NOT SET>```
* Identify the Active Directory user objects that are currently a member of a Protected Group
* Identify the Protected Groups the Active Directory user object is a direct member of
* Identify the Protected Groups the Active Directory user object is a transitive member of
* Identify if the user object is a member of the Protected Users group

#### Get-ElevatedADUsers.ps1
This script is written in PowerShell and requires the ActiveDirectory module. It is designed to be run from a domain joined Privileged Workstation or other domain joined computer that has RSAT installed.

The script has two optional parameters.

1. ```$ReportLocation``` - If this parameter is not specified, the value for ```$ReportLocation``` will be set to ```"$env:USERPROFILE\Desktop\"```.

2. ```$File``` - If this paramter is not specified, the value for ```$File``` will be set to ```"Get-ElevatedADUsers_Report_$(Get-Date -Format ddMMyyyy_HHMMss).txt"```.

The two optional switches are:

1. ```-IncludeDisabled``` - This sets the variable ```$ElevatedUsers``` to ```"Get-ADUser -Filter {(adminCount -ne 0)}"```.

2. ```-LookCool``` - I added this becase the switch ```-Verbose``` is reserved by PowerShell v2.

> If the optional switch ```-IncludeDisabled``` is omitted, the variable ```$ElevatedUsers``` is set to ```Get-ADUser -Filter {(Enabled -eq "true") -and (adminCount -ne 0)}```.

##### Examples
```Get-ElevatedADUsers.ps1```

```Get-ElevatedADUsers.ps1 -IncludeDisabled```

```Get-ElevatedADUsers.ps1 -LookCool```

##### Seeing if an Active Directory user object is a member of a protected group
As an Active Directory user object can be a member of more than one protected group, I used an array ```$MembershipinElevatedGroups``` to note the protected groups an Active Directory user object is a member of. The code below adds "Enterprise Admins" to the ```$MembershipinElevatedGroups``` array if the Active Directory user object is a direct member of the "Enterprise Admins" group.

```If (((Get-ADUser -Identity $ElevatedUser -Properties memberOf).memberOf) -like "*Enterprise Admins*"){```
        ```$MembershipinElevatedGroups += "Enterprise Admins"```
    ```}```

This does not account for an Active Directory user object that is a transient member of a elevated group. Suppose an Active Directory user object is not a member of "Domain Admins" but is a member of "Helpdesk", and that "Helpdesk" is a member of "Domain Admins", I wanted the script to identify the Active Directory user object that is a member of "Helpdesk" since that group is a member of the "Domain Admins" group, which provides the Active Directory user object with elevated privileges. I came across the article, ["PowerTip: User PowerShell to Find if User Is Nested Group Member"](https://devblogs.microsoft.com/scripting/powertip-use-powershell-to-find-if-user-is-nested-group-member/) by Microsoft and was able to learn what I needed to identify the Active Directory user object that is a member of an Active Directory security group and the group is a member of a protected group (e.g. the Active Directory user object is a member of the "Helpdesk" group and the "Helpdesk" group is a member of the "Domain Admins" group). I created another array, this one is named ```$MembershipinElevatedNestedGroups``` to note the protected groups an Active Directory user object is a nested member of. The code below adds "Domain Admins" to the ```$MembershipinElevatedNestedGroups``` array if the Active Directory user object is a nested member of the "Domain Admins" group.

```If (Get-ADUser -Filter "memberOf -RecursiveMatch '$((Get-ADGroup "Domain Admins").DistinguishedName)'" -SearchBase ((Get-ADUser -Identity $ElevatedUser)DistinguishedName)){```
        ```If ($MembershipinElevatedGroups -notcontains "Domain Admins"){```
            ```$MembershipinElevatedNestedGroups += "Domain Admins"```
        ```}```
    ```}```

An open issue is dealing with an Active Directory user object which is both a member of a particular elevated group directly and through nested group membership. The code ```If (Get-ADUser -Filter "memberOf -RecursiveMatch '$((Get-ADGroup "Domain Admins").DistinguishedName)'" -SearchBase ((Get-ADUser -Identity $ElevatedUser)DistinguishedName)){``` returns Active Directory user objects with direct or nested group membership in the "Domain Admins" group. Earlier in the script, the code ```If (((Get-ADUser -Identity $ElevatedUser -Properties memberOf).memberOf) -like "*Domain Admins*"){```
        ```$MembershipinElevatedGroups += "Domain Admins"```
    ```}```.
That code returns Active Directory user objects which are a direct member of the "Domain Admins" group.

To address that, I added the code ```If ($MembershipinElevatedGroups -notcontains "Domain Admins"){```
            ```$MembershipinElevatedNestedGroups += "Domain Admins"```
        ```}```.

That evalutes if the Active Directory user object is a direct member of the "Domain Admins" group. If "Domain Admins" is already in the ```$MembershipinElevatedGroups``` array, then it is not also added to the ```$MembershipinElevatedNestedGroups``` array. That does not mean an Active Directory user object could not be both a member of a particular elevated group directly and through nested group membership.

> If you find an Active Directory user object which is a member of an elevated group directly that should not be, after you remove the object from the elevated group, you should run this script again to determine if the Active Directory user object is also a member of the elevated group through nested group membership.

##### Download Get-ElevatedADUsers
Click [Get-ElevatedADUsers](https://github.com/4D5A/Active-Directory-Tools/blob/main/Security%20Tools/Get-ElevatedADUsers.ps1) to download Get-ElevatedADUsers.

### Footnotes
[1] [https://devblogs.microsoft.com/scripting/powertip-use-powershell-to-find-if-user-is-nested-group-member/](https://devblogs.microsoft.com/scripting/powertip-use-powershell-to-find-if-user-is-nested-group-member/)

[2] [https://social.technet.microsoft.com/wiki/contents/articles/22331.adminsdholder-protected-groups-and-security-descriptor-propagator.aspx](https://social.technet.microsoft.com/wiki/contents/articles/22331.adminsdholder-protected-groups-and-security-descriptor-propagator.aspx)

[3] [2022-09-02-active-directory-elevated-users-health-check](2022-09-02-active-directory-elevated-users-health-check)

[4] [2022-09-02-active-directory-elevated-users-health-check](2022-09-02-active-directory-elevated-users-health-check)

[5] [https://docs.microsoft.com/en-us/windows-server/identity/ad-ds/manage/how-to-configure-protected-accounts#BKMK_Prereq](https://docs.microsoft.com/en-us/windows-server/identity/ad-ds/manage/how-to-configure-protected-accounts#BKMK_Prereq)

[6] [https://docs.microsoft.com/en-us/windows-server/identity/ad-ds/manage/how-to-configure-protected-accounts#BKMK_Prereq](https://docs.microsoft.com/en-us/windows-server/identity/ad-ds/manage/how-to-configure-protected-accounts#BKMK_Prereq)