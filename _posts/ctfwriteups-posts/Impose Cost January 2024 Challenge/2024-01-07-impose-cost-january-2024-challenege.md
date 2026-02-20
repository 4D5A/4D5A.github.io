---
layout: post
title: Impose Cost January 2024 Challenge
thumbnail-img: 'assets/img/2024-01-07-impose-cost-january-2024-challenge/2024-01-07-impose-cost-january-2024-challenge-screenshot.png'
gh-repo: 4D5A
gh-badge: [follow]
categories: [ctfwriteups]
event: impose-cost-january-2024-challenege
tags: [ImposeCost-CTF,ctf-decoding]
after-content: [disclaimer-notice.html]
---
### Explanation
On January 6, 2024, [@ImposeCost](https://twitter.com/ImposeCost/status/1743691690588647491) posted a challege to decode text and obtain the "flag".

<img src="{{ 'assets/img/2024-01-07-impose-cost-january-2024-challenge/2024-01-07-impose-cost-january-2024-challenge-screenshot.png' | relative_url }}" alt='Impose Cost January 2024 Challenge Screenshot' />

### Solving the Challenge
They way I solved this challenge is *not* the intended method. I missed an important step but I was still able to solve the challenge. Hopefully this helps someone else.

Original encoded text ->

```48 34 73 49 41 4c 53 53 6d 57 55 41 2f 77 33 4c 6a 5a 6e 47 45 41 77 41 34 4a 32 6f 41 66 79 6b 46 45 6b 2b 48 72 32 51 2f 51 66 70 44 66 44 65 54 67 73 63 44 54 44 4f 38 6b 38 48 4a 49 31 52 47 6a 78 30 76 2f 67 61 33 36 67 42 53 70 76 56 4a 63 43 6c 67 65 6e 6b 66 4e 57 41 59 51 71 59 6e 68 6a 47 7a 6d 6f 72 33 43 64 37 71 77 48 72 33 49 38 71 6f 36 72 34 62 6a 4f 36 75 65 4e 6c 43 39 49 73 34 76 52 48 39 43 66 53 4a 78 64 61 41 72 6f 79 6a 79 48 34 37 35 6a 57 35 69 74 35 63 71 5a 45 54 65 75 48 51 34 78 70 2f 41 47 2f 44 75 4b 65 6f 41 41 41 41 41 3d 3d```

1. Looking at the encoded text, it appears to be hexadecimal. The characters are "a" through "f", "A" through "F", and "0" through "9". Let's use [CyberChef](https://gchq.github.io/CyberChef/) to start decoding the text, use "From Hex" and paste the encoded text in the Input box.

    <img src="{{ 'assets/img/2024-01-07-impose-cost-january-2024-challenge/2024-01-07-impose-cost-january-2024-challenge-from-hex-screenshot.png' | relative_url }}" alt='Impose Cost January 2024 Challenge from hex.png' />

    Step 1 decoded text ->

    ```H4sIALSSmWUA/w3LjZnGEAwA4J2oAfykFEk+Hr2Q/QfpDfDeTgscDTDO8k8HJI1RGjx0v/ga36gBSpvVJcClgenkfNWAYQqYnhjGzmor3Cd7qwHr3I8qo6r4bjO6ueNlC9Is4vRH9CfSJxdaAroyjyH475jW5it5cqZETeuHQ4xp/AG/DuKeoAAAAA==```

2. Looking at the Ouput, the text appears to be base64. Something that gives us a clue that it is base64 is the use of the "=" for padding at the end of the string.

    <img src="{{ 'assets/img/2024-01-07-impose-cost-january-2024-challenge/2024-01-07-impose-cost-january-2024-challenge-base64-screenshot.png' | relative_url }}" alt='Impose Cost January 2024 Challenge base64.png' />

    I added the "From Base64" Operation and that shows up new output.

    <img src="{{ 'assets/img/2024-01-07-impose-cost-january-2024-challenge/2024-01-07-impose-cost-january-2024-challenge-from-base64-screenshot.png' | relative_url }}" alt='Impose Cost January 2024 Challenge from base64.png' />

    Step 2 decoded text ->

    ```ÆÎj+Ü'{«ëÜ*£ªøn3º¹ãeÒ,âôGô'Ò'Zº2!øïÖæ+yr¦DMëCiü¿â ```

    We need to determine what type of file this is. You can do that by checking the header. This header might not be easy to quickly identify if you haven't encountered this type of file in the past. CyberChef offers multiple ways to determine what type of file this is. One method is to use the "Magic" Operation which will take the output and show you multiple possible new outputs and which Operations will provide each result. Another method is to click the "Magic Wand" button. Both methods show the file is likely compressed using Gzip.

3. Use the Gunzip Operation

    <img src="{{ 'assets/img/2024-01-07-impose-cost-january-2024-challenge/2024-01-07-impose-cost-january-2024-challenge-gunzip-screenshot.png' | relative_url }}" alt='Impose Cost January 2024 Challenge gunzip.png' />

    Step 3 decoded text ->

    ```F5ZHEYZBEQYTAILEMNZCCWKEINFUMU2AKNKEMWKRJ5DEMTZBONYGG4JBMBRWE2LDOEQXGZ3JEFYGA3ZBMJRXIZZOMZZWAL3GM5RXC43HMNRHW5ZPNNVWWLROHNTWEZTGOQQWM3ZBONTXO4DAN52HCZDTPMQW22KO```

4. You might recognize the value as Base32 or you can use the "Magic" Operation or click the "Magic Wand" button.

    <img src="{{ 'assets/img/2024-01-07-impose-cost-january-2024-challenge/2024-01-07-impose-cost-january-2024-challenge-from-base32-screenshot.png' | relative_url }}" alt='Impose Cost January 2024 Challenge base32.png' />

    Step 4 decoded text ->

    ```/rrc!$10!dcr!YDCKFS@STFYQOFFO!spcq!`cbicq!sgi!p`o!bctg.fs`/fgcqsgcb{w/kkk..;gbfft!fo!sgwp`otqds{!miN```

5. This is where I initially got stuck. I used https://www.dcode.fr/cipher-identifier to determine what type of cipher may have encoded the text. The website tells us the cipher is most likely ROT (you could also use an Affine cipher).

    <img src="{{ 'assets/img/2024-01-07-impose-cost-january-2024-challenge/2024-01-07-impose-cost-january-2024-challenge-detect-cipher-screenshot.png' | relative_url }}" alt='Impose Cost January 2024 Challenge detect cipher.png' />

    We can see the characters "www." in the decoded text so we might be headed in the right direction.

    <img src="{{ 'assets/img/2024-01-07-impose-cost-january-2024-challenge/2024-01-07-impose-cost-january-2024-challenge-rot-cipher-result-screenshot.png' | relative_url }}" alt='Impose Cost January 2024 Challenge rot cipher result.png' />

    <img src="{{ 'assets/img/2024-01-07-impose-cost-january-2024-challenge/2024-01-07-impose-cost-january-2024-challenge-affine-cipher-result-screenshot.png' | relative_url }}" alt='Impose Cost January 2024 Challenge affine cipher result.png' />

    Both ciphers show a character shift of 14. I added the Affine Cipher Decode Operation in CyberChef and pasted the value from dcode.fr.

    <img src="{{ 'assets/img/2024-01-07-impose-cost-january-2024-challenge/2024-01-07-impose-cost-january-2024-challenge-affine-cipher-decode-screenshot.png' | relative_url }}" alt='Impose Cost January 2024 Challenge affine cipher decode.png' />

    Step 5 decoded text ->

    ```/ddo!$10!pod!KPOWRE@EFRKCARRA!eboc!`onuoc!esu!b`a!nofs.re`/rsoceson{i/www..;snrrf!ra!esib`afcpe{!yuZ```

6. The text I decoded in Step 5 includes the string "www." but let's go back to the challenge and think about what we are looking for.

     <img src="{{ 'assets/img/2024-01-07-impose-cost-january-2024-challenge/2024-01-07-impose-cost-january-2024-challenge-description.png' | relative_url }}" alt='Impose Cost January 2024 Challenge description.png' />

     Could we be looking for "www." be part of a string related to purchasing merchandise from imposecost.net? I went to www.imposecost.net and clicked on "Shop". That link goes to https://www.imposecost.net/shop. I looked at the encoded text and thought it looked like the text might be reversed. I used the "Reverse Operation" in CyberChef and reviewed the output.

    <img src="{{ 'assets/img/2024-01-07-impose-cost-january-2024-challenge/2024-01-07-impose-cost-january-2024-challenge-reverse-screenshot.png' | relative_url }}" alt='Impose Cost January 2024 Challenge reverse.png' />

    Step 6 decoded text ->

    ```Zuy!{epcfa`bise!ar!frrns;..www/i{nosecosr/`er.sfon!a`b!use!couno`!cobe!ARRACKRFE@ERWOPK!dop!01$!odd/```

7. I missed a step and I didn't know what it was, so I decided try to manually brute force the code. :)

    <img src="{{ 'assets/img/2024-01-07-impose-cost-january-2024-challenge/2024-01-07-impose-cost-january-2024-challenge-brute-force-screenshot.png' | relative_url }}" alt='Impose Cost January 2024 Challenge brute force.png' />

    It was clear that the flag was a coupon code and that going back and by skipping a specific number of characters in the string would result in me arrving at one or more consecutive characters in cleartext (which should have told what step in the process I misssed was). The coupond code was somewhere in the following string.

    ```ARRACKRFE@ERWOPK!dop!01$!odd/```

8. "ARRACK" almost looks like ATTACK. I went to Twitter and searched for the keywords ImposeCost and attack.

    <img src="{{ 'assets/img/2024-01-07-impose-cost-january-2024-challenge/2024-01-07-impose-cost-january-2024-challenge-imposecost-attack-screenshot.png' | relative_url }}" alt='Impose Cost January 2024 Challenge imposecost attack.png' />

    Does "attack the network" fit what is known about the encoded text?

    <img src="{{ 'assets/img/2024-01-07-impose-cost-january-2024-challenge/2024-01-07-impose-cost-january-2024-challenge-brute-force-attack-the-network-screenshot.png' | relative_url }}" alt='Impose Cost January 2024 Challenge brute force attack the network.png' />

    ```ATTACKTHENETWORK``` is the correct answer!

Thank you to [@ImposeCost](https://twitter.com/ImposeCost) for designing this challenge. Next time I see that skipping a specific number of characters in a string results in me arriving at one or more consecutive characters in cleartext, I will remember to XOR the string.



