---
layout: post
title: Blue Team Con 2022 CTF - Pixels
gh-repo: 4D5A
gh-badge: [follow]
categories: [ctfwriteups]
event: blue-team-con-2022-last-minute-ctf
tags: [BlueTeamCon2022-CTF,ctf-forensics]
after-content: [disclaimer-notice.html]
---
### Explanation
In the Pixels Challenge, you are given a blurry photo. If you zoom out enough, you can see the image becomes more clear and you can start to make out letters that are consistent with the format of a flag.

### Solving the Challenge
1. Browse to http://18.205.188.77/challenges.
2. Click Pixels
3. Click pixels.png
4. Use paint.NET to open the picture pixels.png.
5. The picture is too blurry to read, so zoom out.
6. At approximately 6% of its actual size, I can make out the words "better than a" and at approximately 7% of its actual size I can make out what could be "mouthful of nails"
7. Use Google to search for quote mouthful nails
8. Click the search result for https://britishsass.tumblr.com/.
9. There is a quote on the website that reads, "slightly better than a mouthful of nails!"
10. Review the picture. We can see that the first letter of the first word is larger than the remaining letters, so we will assume it is a capital "S". The second to last character does not seem to be an exclamation mark, but could be a period.
11. Through trial and error, I confirmed my assumptions in the previous step.
12. The flag is BTC{Slightly better than a mouthful of nails.}
13. Paste the flag into the Flag field.
14. Click Submit.