---
layout: post
title: Blue Team Con 2022 CTF - Rhymes
gh-repo: 4D5A
gh-badge: [follow]
categories: [ctfwriteups]
event: blue-team-con-2022-last-minute-ctf
tags: [BlueTeamCon2022-CTF,ctf-forensics]
after-content: [disclaimer-notice.html]
---
### Explanation
In the Rhymes Challenge, you are provided with a PDF. It contains two lines of redacted text. The first line of redacted text is on page 2. The second line of redacted text is on line 4.

There are several ways you can uncover the "redacted" text in the PDF. This demonstrates the problems with highlighting in a dark color as a form of "redacting". Someone else can highlight the "redacted" text, copy it, and paste it into a different program like a text editor. Because the original text was not replaced but was only covered with what is essentially black highlighting which is added as a new layer, it can be copy and pasted into a different document.

If you got the flag BTC[Mots D'Heures: Grousses, Rames] and did not receive points for it, that is because it is not the flag. The Rules at http://18.205.188.77/rules tell us that flags will use the format, ```BTC{Flag_goes_here}```.

If you go to page 4, there is a second line of "redacted" text which contains the actual flag.


### Solving the Challenge
1. Browse to http://18.205.188.77/challenges.
2. Click Rhymes.
3. Click Rhymes.pdf to download the document.
4. Open Rhymes.pdf in a PDF reader.
5. Use your cursor to highlight the redacted content located at the bottom of page 4 (under section 30).
6. Copy the redacted text.
7. Paste the content into the Flag field in http://18.205.188.77/challenges#Rhymes-14. The flag is BTC{AND-now-YOU-know-MULTIPLE-languages}
8. Click Submit.

<img src="{{ 'assets/img/2022-09-01-btc-2022-last-minute-ctf-rhymes/btc-2022-last-minute-ctf-ryhmes-screenshot.png' | relative_url }}" alt='BTC 2022 Last Minute CTF Rhymes Screenshot' />