---
layout: post
title: Blue Team Con 2022 CTF - Paranoia
gh-repo: 4D5A
gh-badge: [follow]
categories: [ctfwriteups]
event: blue-team-con-2022-last-minute-ctf
tags: [BlueTeamCon2022-CTF,ctf-forensics]
after-content: [disclaimer-notice.html]
---
### Explanation
In the Paranoia Challenge, we are provided with a zip file and told to "Please utilize the information provided in their archive to see if you can find any additional informtion on the flag And then...". The phrase "additional information" is a clue.

When I opened the zip file, I noticed there is a directory named "_MACOSX". Since we need to look for "additional information" and we were provided a zip file, so I looked for a .DS_Store file.

<img src="{{ 'assets/img/2022-09-01-btc-2022-last-minute-ctf-paranoia/btc-2022-last-minute-ctf-paranoia-archive-directory-list-screenshot.png' | relative_url }}" alt='BTC 2022 Last Minute CTF Paranoia Archive Directory List Screenshot' />

I opened Paranoia and found a .DS_Store file. I opened the file and it contained the flag.

<img src="{{ 'assets/img/2022-09-01-btc-2022-last-minute-ctf-paranoia/btc-2022-last-minute-ctf-paranoia-screenshot.png' | relative_url }}" alt='BTC 2022 Last Minute CTF Paranoia Screenshot' />

### Solving the Challenge

1. Browse to http://18.205.188.77/challenges.
2. Click Paranoia
3. Click Paranoia.zip
4. Using 7zip, open the archive Paranoia.zip.
5. Double click Paranoia.
6. Open .DS_Store in a text editor.
7. Copy the string B T C { b 5 1 5 0 3 f b 0 f 1 f 2 7 a d 0 3 7 a 9 d 6 d 9 5 a a 7 0 e 7 } and paste it into a text editor
8. Remove the spaces between each character in the string.
9. The flag is BTC{b51503fb0f1f27ad037a9d6d95aa70e7}
10. Paste the flag into the Flag field.
11. Click Submit.