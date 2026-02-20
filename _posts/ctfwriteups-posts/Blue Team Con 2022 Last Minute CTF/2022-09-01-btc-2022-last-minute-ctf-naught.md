---
layout: post
title: Blue Team Con 2022 CTF - Naught
gh-repo: 4D5A
gh-badge: [follow]
categories: [ctfwriteups]
event: blue-team-con-2022-last-minute-ctf
tags: [BlueTeamCon2022-CTF,ctf-forensics]
after-content: [disclaimer-notice.html]
---
### Explanation
In the Naught Challenge, you are given an XLSX document that contains a "redacted" flag. The description for the challenge reads, "The Last Minute CTF has redacted the flag from this document. Please recover it." The use of the word "recover" suggests that perhaps information has been deleted in the file.

The file extension XLSX is associated with the XML based Excel file format. If we open the document, we see there is Sheet1 and Sheet3 but not Sheet2.

Our challenge seems to be related to recovering information located in Sheet2. Since XLSX files can be opened in the same was as a compressed file, I choose to use 7zip to open it.

In Naught.xlsx\xl\worksheets, we see sheet1.xml, sheet2.xml, and sheet3.xml.

<img src="{{ 'assets/img/2022-09-01-btc-2022-last-minute-ctf-naught/btc-2022-last-minute-ctf-naught-files-screenshot.png' | relative_url }}" alt='BTC 2022 Last Minute CTF Naught Files Screenshot' />

If we open sheet2.xml in a text editor, there is content that apears to be arranged in rows and columns which is consistent with that we would expect to see with a spreadsheet.

<img src="{{ 'assets/img/2022-09-01-btc-2022-last-minute-ctf-naught/btc-2022-last-minute-ctf-naught-sheet2-xml-screenshot.png' | relative_url }}" alt='BTC 2022 Last Minute CTF Naught sheet2.xml Screenshot' />

Since information in the xlsx file appears to be corrupted, I deleted sheet1.xml in the xlsx file and renamed sheet2.xml to sheet1.xml since the xlsx file already has the required information about sheet1.xml to display its data.

Now Naught.xlsx can be opened and we can access the data in what was sheet2 in the the new sheet1.

<img src="{{ 'assets/img/2022-09-01-btc-2022-last-minute-ctf-naught/btc-2022-last-minute-ctf-naught-sheet1-screenshot.png' | relative_url }}" alt='BTC 2022 Last Minute CTF Naught Sheet1 Screenshot' />

Each row has one cell hightlighted. Starting with the first row, I made a list of the values.

```SHA-256```
```You```
```Incorrect```
```Yellow```
```Echo```
```Lima```
```Tango```
```Omega```
```Chelsea```
```Spaces```

From those entries, it looks like we need to use SHA-256 to generate the hash value of those words as a string. The last highlighted cell is the word "Spaces" so we will add spaces between each word when we create the string to generate its hash value.

We can use a number of tools to calculate hash values of strings but in this case, I used [CyberChef](https://gchq.github.io/).

Since SHA-256 is really SHA2 using a size of 256, we will add SHA2 to our Recipe, select a 256 from the drop down box for the Size, and enter the words from Sheet1 with spaces between them.

[https://gchq.github.io/CyberChef/#recipe=SHA2('256',64,160)&input=WW91IEluY29ycmVjdCBZZWxsb3cgRWNobyBMaW1hIFRhbmdvIE9tZWdhIENoZWxzZWE](https://gchq.github.io/CyberChef/#recipe=SHA2('256',64,160)&input=WW91IEluY29ycmVjdCBZZWxsb3cgRWNobyBMaW1hIFRhbmdvIE9tZWdhIENoZWxzZWE)

CyberChef outputs the value ```a7f79674938f6e62fcce7c5aac4eaf3581fceffb88d7d17b83c72895a54aa0d7```. When we add that between the curly brackets of BTC{}, it becomes BTC{a7f79674938f6e62fcce7c5aac4eaf3581fceffb88d7d17b83c72895a54aa0d7} and that is our flag.

<img src="{{ 'assets/img/2022-09-01-btc-2022-last-minute-ctf-naught/btc-2022-last-minute-ctf-naught-screenshot.png' | relative_url }}" alt='BTC 2022 Last Minute CTF Naught Screenshot' />


### Solving the Challenge
1. Browse to http.//18.205.188.77/challenges.
2. Click Naught
3. Click Naught.xlsx to download the document.
4. Using 7zip, open Naught.xlsx.
5. Double click xl
6. Double click worksheets
7. Delete sheet1.xml
8. Rename sheet2.xml to sheet1.xml
9. Close Naught.xlsx that is open in 7zip.
10. Open Naught.xlsx in Excel.
11.  When Excel prompts you if you want to try to recovery information, choose Yes.
12. When Excel displays information on the repairs it made to the file, click Close.
13. Click Sheet1
14. Browse to https.//gchq.github.io/CyberChef/
15. Sheet1 shows the hashing algorithm is SHA-256 (which is SHA2 with a size of 256), so drag SHA2 to the Recipe.
16. Click Size and click 256.
17. Type the highlighted words beginning with "You" from top to bottom into the Input for CyberChef. The last row of text has the word "Spaces" highlighted, so separate your words in the Input for CyberChef with spaces.
18. Your value for the Input in CyberChef should be "Your Incorrect Yellow Echo Lima Tango Chelsea" (without quotation marks).
19. Copy and paste the Output in CyberChef into Notepad. The Output in CyberChef will be a7f79674938f6e62fcce7c5aac4eaf3581fceffb88d7d17b83c72895a54aa0d7
20. Add "BTC{" (without quotes) to the string in Notepad.
21. Add "}" (without quotes) to the string in Notepad.
22. The flag is BTC{a7f79674938f6e62fcce7c5aac4eaf3581fceffb88d7d17b83c72895a54aa0d7}
23. Paste the flag into the Flag field.
24. Click Submit.

<img src="{{ 'assets/img/2022-09-01-btc-2022-last-minute-ctf-naught/btc-2022-last-minute-ctf-naught-screenshot.png' | relative_url }}" alt='BTC 2022 Last Minute CTF Naught Screenshot' />