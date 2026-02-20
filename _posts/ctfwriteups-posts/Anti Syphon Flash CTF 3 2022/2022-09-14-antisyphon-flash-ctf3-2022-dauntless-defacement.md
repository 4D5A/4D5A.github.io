---
layout: post
title: Antisyphon Flash CTF 3 - Dauntless Defacement
thumbnail-img: 'assets/img/2022-09-14-antisyphon-flash-ctf3-2022-defacement/antisyphon-flash-ctf3-2022-defacement-wireshark-tcp-stream-8-screenshot.png'
gh-repo: 4D5A
gh-badge: [follow]
categories: [ctfwriteups]
event: anti-syphon-flash-ctf-3-2022
tags: [Antisyphon-Flash-CTF3,ctf-forensics]
after-content: [disclaimer-notice.html]
---
### Explanation
Examine a packet capture to locate a flag.

### Solving the Challenge
1. Download defacement.pcap and open it with Wireshark
2. According to the challenge description, we are examining a packet capture that includes network traffic created while an attacker defaced a website so we are going to apply a Wireshark display filter for http.
3. To follow the TCP stream, right click the first packet, click Follow, and click TCP Stream.

    <img src="{{ 'assets/img/2022-09-14-antisyphon-flash-ctf3-2022-defacement/antisyphon-flash-ctf3-2022-defacement-wireshark-http-packets-screenshot.png' | relative_url }}" alt="Antisyphon Flash CTF #3 2022 Dauntless Defacement TCP Stream 0 Screenshot" />

4. Use the up arrow for Stream in the bottom right corner of the window to move through each stream.

    <img src="{{ 'assets/img/2022-09-14-antisyphon-flash-ctf3-2022-defacement/antisyphon-flash-ctf3-2022-defacement-wireshark-navigate-streams-screenshot.png' | relative_url }}" alt="Antisyphon Flash CTF #3 2022 Dauntless Defacement Streams Screenshot" />

5. Stream 8 shows what appears to be an attacker attempting to obtain a shell and use that to make a nc connection to a C2 server with the IP address 192.168.1.197.

    <img src="{{ 'assets/img/2022-09-14-antisyphon-flash-ctf3-2022-defacement/antisyphon-flash-ctf3-2022-defacement-wireshark-tcp-stream-8-screenshot.png' | relative_url }}" alt="Antisyphon Flash CTF #3 2022 Dauntless Defacement TCP Stream 8 Screenshot" />

6. In Stream 9, we see what appears to be an attacker downloading a file named hack.zip from hxxp://192.168.1.197:2446/hack.zip.

    <img src="{{ 'assets/img/2022-09-14-antisyphon-flash-ctf3-2022-defacement/antisyphon-flash-ctf3-2022-defacement-wireshark-tcp-stream-9-part-1-screenshot.png' | relative_url }}" alt="Antisyphon Flash CTF #3 2022 Dauntless Defacement TCP Stream 9: Part 1 Screenshot" />

7. Looking through Stream 9, we see the attacker unzip hack.zip and enter the password ```ASup3rL33tUncr4ckabl3P4ssw0rd```.

    <img src="{{ 'assets/img/2022-09-14-antisyphon-flash-ctf3-2022-defacement/antisyphon-flash-ctf3-2022-defacement-wireshark-tcp-stream-9-part-2-screenshot.png' | relative_url }}" alt="Antisyphon Flash CTF #3 2022 Dauntless Defacement TCP Stream 9: Part 2 Screenshot" />

8. By clicking File, Export Objects, and HTTP..., we can look for HTTP objects in the packet capture.
9. Select the HTTP object for the zip file.

    <img src="{{ 'assets/img/2022-09-14-antisyphon-flash-ctf3-2022-defacement/antisyphon-flash-ctf3-2022-defacement-wireshark-http-object-list-screenshot.png' | relative_url }}" alt="Antisyphon Flash CTF #3 2022 Dauntless Defacement HTTP Object list Screenshot" />

10. Use 7zip to open hack.zip

    <img src="{{ 'assets/img/2022-09-14-antisyphon-flash-ctf3-2022-defacement/antisyphon-flash-ctf3-2022-defacement-secret-txt-screenshot.png' | relative_url }}" alt="Antisyphon Flash CTF #3 2022 Dauntless Defacement List of files in the zip file Screenshot" />

11. Enter the password ```ASup3rL33tUncr4ckabl3P4ssw0rd```.
12. The flag is located in secret.txt.