---
layout: post
title: Blue Team Con 2022 CTF - Nonsense
gh-repo: 4D5A
gh-badge: [follow]
categories: [ctfwriteups]
event: blue-team-con-2022-last-minute-ctf
tags: [BlueTeamCon2022-CTF,ctf-codes-and-ciphers]
after-content: [disclaimer-notice.html]
---
### Explanation
In the Nonsense Challenge, we are given the file, nonsense.code.

I opened the file in a text editor, and right away the string ```tnS\n31TOR``` stood out to me. ```31TOR``` is "ROT13" backwards, so I realized I probably needed to use ROT13 to decode a string. ```\n``` is a regular expression for a new line. I kept reading from right to left and it continued, ```Synt = ZQ5```. I used ROT13 to decode the string "5QZ = tnyS" and that produced the string "5DM = galF" which is "Flag = MD5" backwards. So, that indicated I needed to continue using ROT13 to decode strings from right to left (read ```31TOR``` from right to left, move to the next section of text to the right, re-write ```5QZ = tnyS``` as ```Synt = ZQ5```, decode ```Synt = ZQ5``` with ROT13, then re-write ```fjbeorlr ba ugvj anvpvegpryr an gfheg erirA``` as ```Arire gehfg na ryrpgevpvna jvgu ab rlroebjf```). That decodes Flag = MD5 Never trust an electrican with no eyebrows.

I used MD5 to hash the string "Never trust an electrian with no eyebrows" and that gave me the hash value 4a9383ffc07d95ab0915b4c6377376b7.

### Solving the Challenge
1. Browse to http://18.205.188.77/challenges.
2. Click Nonsense
3. Click nonsense.code to download the file.
4. Use a Notepad to open nonsense.code.
5. Browse to https://gchq.github.io
6. Copy and paste the string 'fjbeorlr ba ugvj anvpvegpryr an gfheg erirA' 5QZ = tnyS\n31TOR present in nonsense.code into the Input field for CyberChef
7. Since I noticed the string "31TOR" which is the reverse value of ROT13, I added ROT13 to the CyberChef Recipe.
8. The Output field for CyberChef showed a sentence but it is the reverse value, so I added reverse to the CyberChef Recipe.
9. The Output field for CyberChef is EBG13a\Flag = MD5 'Never trust an electrician with no eyebrows'
10. Edit the Input field for CyberChef so you only have the text between the single quotes. The text in the Input field for CyberChef should be fjbeorlr ba ugvj anvpvegpryr an gfheg erirA
11. Add MD5 to the CyberChef Recipe.
12. Copy and paste the Output field for CyberChef into Notepad
13. Add "BTC{" (without quotes) before the string you pasted into Notepad.
14. Add "}" (without quotes) after the string you pasted into Notepad.
15. The flag is BTC{4a9383ffc07d95ab0915b4c6377376b7}
16. Paste the flag into the Flag field.
17. Click Submit.

<img src="{{ 'assets/img/2022-09-01-btc-2022-last-minute-ctf-nonsense/btc-2022-last-minute-ctf-nonsense-screenshot.png' | relative_url }}" alt='BTC 2022 Last Minute CTF Nonsense Screenshot' />