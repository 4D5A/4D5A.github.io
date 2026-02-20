---
layout: post
title: Blue Team Con 2022 CTF - Tardis v2
gh-repo: 4D5A
gh-badge: [follow]
categories: [ctfwriteups]
event: blue-team-con-2022-last-minute-ctf
tags: [BlueTeamCon2022-CTF,ctf-forensics]
after-content: [disclaimer-notice.html]
---
### Explanation
In the Tardis v2 Challenge, we are given a rar file and tasked with obtaining a hidden flag.

If you extract the file in flag.rar, you will only be given a file named, "flag.txt". In the file, it says, "Nope it's not here. You'll need to find an alternate location." That is a hint that the flag is contained in an Alternate Data Stream.

<img src="{{ 'assets/img/2022-09-01-btc-2022-last-minute-ctf-tardis-v2/btc-2022-last-minute-ctf-tardis-v2-extracted-screenshot.png' | relative_url }}" alt='BTC 2022 Last Minute CTF Tardis v2 Extracted Screenshot' />

You can access the data in the Alternate Data Stream with 7zip.

<img src="{{ 'assets/img/2022-09-01-btc-2022-last-minute-ctf-tardis-v2/btc-2022-last-minute-ctf-tardis-v2-alternate-data-steam-using-7zip.png' | relative_url }}" alt='BTC 2022 Last Minute CTF Tardis v2 Alternate Data Stream Using 7zip Screenshot' />

If you are on Windows, you can also use Command Prompt to look for Alternate Data Streams and read data in them.

To see if a file contains an Alternate Data Steam, you can use the following command:

```dir /r file```

<img src="{{ 'assets/img/2022-09-01-btc-2022-last-minute-ctf-tardis-v2/btc-2022-last-minute-ctf-tardis-v2-alternate-data-steam-using-command-prompt.png' | relative_url }}" alt='BTC 2022 Last Minute CTF Tardis v2' />

### Solving the Challenge
1. Browse to http://18.205.188.77/challenges.
3. Click Tardis v2
4. Click flag.rar to download the file.
5. Using 7zip, open the archive flag.rar.
6. Double click flag.txt:nothereeither.txt.
7. The flag is BTC{AlternateLocationHazBeenIdentified!}
8. Copy the flag
9. Paste the flag into the Flag field.
10. Click Submit.