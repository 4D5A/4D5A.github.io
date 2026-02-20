---
layout: post
title: Antisyphon Flash CTF 3 - Just My Kind of Type
thumbnail-img: 'assets/img/2022-09-14-antisyphon-flash-ctf3-2022-just-my-kind-of-type/antisyphon-flash-ctf3-2022-just-my-kind-of-type-screenshot.png'
gh-repo: 4D5A
gh-badge: [follow]
categories: [ctfwriteups]
event: anti-syphon-flash-ctf-3-2022
tags: [Antisyphon-Flash-CTF3,ctf-web-exploitation]
after-content: [disclaimer-notice.html]
---
### Explanation
Exploit a vulnerable PHP file upload page to view the contents of /flag.txt.

### Solving the Challenge
1. The upload only allows PNG file types.
2. If we add ".png" to the end of a php file (so the file name ends in .php.png), the upload will permit the file.
3. The upload also appears to restrict the length of the file.
4. I opened a PNG file and removed the information except for the header and the size information, then added the PHP code to output the contents of /flag.txt:

    ```<?php```
    ```echo file_get_contents( "/flag.txt" );```
    ```?>```

5. Upload the file.
6. Visit the profile page.
7. Right click the profile page, and click Inspect.
8. Find the URI for the profile picture.
9. Open a new tab.
10. Browse to the URI for the profile picture.

<img src="{{ 'assets/img/2022-09-14-antisyphon-flash-ctf3-2022-just-my-kind-of-type/antisyphon-flash-ctf3-2022-just-my-kind-of-type-screenshot.png' | relative_url }}" alt='Antisyphon Flash CTF #3 2022 Just My Kind of Type Screenshot' />