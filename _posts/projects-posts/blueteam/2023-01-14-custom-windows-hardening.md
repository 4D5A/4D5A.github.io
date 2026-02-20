---
layout: post
title: Custom Windows Hardening
gh-repo: 4D5A/windows_hardening
gh-badge: [follow, star]
categories: [blueteam, projects]
tags: [Systems-Administration-Tools, infosec]
after-content: [disclaimer-notice.html]
---
## Introduction
[4D5A/windows_hardening](https://github.com/4D5A/windows_hardening) is a personal fork of [0x6d69636b/windows_hardening](https://github.com/0x6d69636b/windows_hardening) by Michael Schneider [0x6d69636b](https://github.com/0x6d69636b). [0x6d69636b/windows_hardening](https://github.com/0x6d69636b/windows_hardening) is a repository used for development and the production repository is [HardeningKitty](https://github.com/scipag/HardeningKitty) by [scipag](https://github.com/scipag). The PowerShell module in the [scipag/HardeningKitty](https://github.com/scipag/HardeningKitty) repository is digitally signed by scipag.<sup>1</sup>

I am not employed by or otherwise affiliated with [0x6d69636b](https://github.com/0x6d69636b) or [scip ag](https://github.com/scipag) other than occassionally contributing to the [0x6d69636b/windows_hardening](https://github.com/0x6d69636b/windows_hardening) repository.

## Overview

Several paid tools offer the ability to scan computers' settings to determine the conformance with security standards such as CIS Benchmarks. I have not found many open source or freeware solutions that allow you to scan computers' settings to determine their conformance with those security standards let alone apply the settings for a standard. 0x6d69636b's windows_hardening solution is such a tool. It is free and open source. It gives infosec professionals the ability to evaluate a computer's configuration to assess its conformance with a specific checklist that is written as a CSV file. 0x6d69636b maintains checklists related to Microsoft, CIS Benchmarks, and other security standards. 0x6d69636b also maintains a custom checklist.

I have also created a custom checklist which I continue to modify.

windows_hardening can be used to check the settings of a computer and to apply settings contained in a checklist. If no checklist is specified, 0x6d69636b's custom checklist is used.

## Install windows_hardening

You can install windows_hardening by downloading the newest release, copying the files, and importing the PowerShell module.

Alternatively, you can run the PowerShell function below which automatically downloads the newest release from the [0x6d69636b/windows_hardening](https://github.com/0x6d69636b/windows_hardening) repository.

```powershell
Function InstallHardeningKitty() {
    $Version = ((Invoke-WebRequest "https://api.github.com/repos/0x6d69636b/windows_hardening/releases/latest" -UseBasicParsing) | ConvertFrom-Json).Name
    $HardeningKittyLatestVersionDownloadLink = ((Invoke-WebRequest "https://api.github.com/repos/0x6d69636b/windows_hardening/releases/latest" -UseBasicParsing) | ConvertFrom-Json).zipball_url
    $ProgressPreference = 'SilentlyContinue'
    Invoke-WebRequest $HardeningKittyLatestVersionDownloadLink -Out HardeningKitty$Version.zip
    Expand-Archive -Path ".\HardeningKitty$Version.zip" -Destination ".\HardeningKitty$Version" -Force
    $Folder = Get-ChildItem .\HardeningKitty$Version | Select-Object Name -ExpandProperty Name
    Move-Item ".\HardeningKitty$Version\$Folder\*" ".\HardeningKitty$Version\"
    Remove-Item ".\HardeningKitty$Version\$Folder\"
    New-Item -Path $Env:ProgramFiles\WindowsPowerShell\Modules\HardeningKitty\$Version -ItemType Directory
    Set-Location .\HardeningKitty$Version
    Copy-Item -Path .\HardeningKitty.psd1,.\HardeningKitty.psm1,.\lists\ -Destination $Env:ProgramFiles\WindowsPowerShell\Modules\HardeningKitty\$Version\ -Recurse
    Import-Module "$Env:ProgramFiles\WindowsPowerShell\Modules\HardeningKitty\$Version\HardeningKitty.psm1"
}
InstallHardeningKitty
```

## Using windows_hardening
windows_hardening has three modes. The modes are Config, Audit, and HailMary. In Config mode, windows_hardening reports on your systems's settings using the checklist provided with the ```FileFindingList``` switch when you run the command (if the ```FileFindingList``` switch is not provided, windows_hardening will use the finding_list_0x6d69636b_machine.csv checklist) as the list of settings to check. In Audit mode, windows_hardening checks your system's settings and compares them with the suggested settings found in the checklist provided with the ```FileFindingList``` (or the default checklist if the ```FileFindingList``` is not provided) and outputs the results to the console. In HailMary mode, windows_hardening configures your system using the settings contained in the checklist provided with the ```FileFindingList``` switch when you run the command. If windows_hardening is run in HailMary mode, it is necessary to explicity select a checklist.

> Use extreme caution if you choose to run windows_hardening in HailMary mode. Because running windows_hardening in HailMary mode will make changes to your system's registry, it is possible that changes to create problems. Be sure to create a backup of your system (and verify the completeness and functionality of your backup) before you run windows_hardening in HailMary mode.

### Config
```powershell
Invoke-HardeningKitty -Mode Config -FileFindingList .\lists\finding_list_0x6d69636b_user.csv -Log -Report
```

### Audit
```powershell
Invoke-HardeningKitty -Mode Audit -FileFindingList .\lists\finding_list_0x6d69636b_user.csv -Log -Report
```

### HailMary
```powershell
Invoke-HardeningKitty -Mode HailMary -FileFindingList .\lists\finding_list_0x6d69636b_user.csv -Log -Report
```

## Custom Checklist
If you are interested in downloading my custom checklist, you can find it at [https://github.com/4D5A/windows_hardening/blob/main/lists/finding_list_4d5a_machine.csv](https://github.com/4D5A/windows_hardening/blob/main/lists/finding_list_4d5a_machine.csv).

You may also choose to clone my fork of windows_hardening. You can clone my fork of windows_hardening by running the command ```git clone https://github.com/4D5A/windows_hardening.git```.

[1] [https://github.com/scipag/HardeningKitty](https://github.com/scipag/HardeningKitty)
