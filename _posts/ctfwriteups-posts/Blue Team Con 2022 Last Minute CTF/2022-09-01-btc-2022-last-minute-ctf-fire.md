---
layout: post
title: Blue Team Con 2022 CTF - Fire
gh-repo: 4D5A
gh-badge: [follow]
categories: [ctfwriteups]
event: blue-team-con-2022-last-minute-ctf
tags: [BlueTeamCon2022-CTF,ctf-forensics]
after-content: [disclaimer-notice.html]
---
### Explanation
In the Fire challenge, we are given the files key4.db and logins.json. These files combined with the name of the challenge being "Fire" is a good hint that we need to crack a firefox password database to obtain the flag.

Fortunately, there is an open source tool that decrypts Mozilla protected passwords. I started with setting up my linux machine for the challenge.

```mkdir -p ~/CTF/Fire```

I saved the key4.db and logins.json files to ~/CTF/Fire.

Next, I am going to change directories to the Fire directory I created.

```cd ~/CTF/Fire```

Next, I used git to clone the firepwd repo.


```git clone https://github.com/lclevy/firepwd.git```

firepwd's readme.md states, I need to use the following command to install the program:

```pip install -r requirements.txt```

pip is a package manager for Python and you can pass the -r argument to specify the location of a file that provides a list of required python packages and their versions to automate the installation.

To install firepwd, I first need to change directory into firepwd.

```cd firepwd```

Now, I need to run the firepwd program and specify the location of the key4.db and logins.json files.

```python3 ./firepwd.py -d ~/CTF/Fire```

The tools provides us with the flag, BTC{key3.dbPassword}.


### Solving the Challenge
1. Run the command mkdir -p ~/CTF/Fire
2. Browse to http://18.205.188.77/challenges.
3. Click Fire.
4. Click key4.db and logins.json to download the files. Save the files in ~/CTF/Fire
5. Run the command cd ~/CTF/Fire
6. Run the command git clone https://github.com/lclevy/firepwd.git
7. Run the command cd firepwd
8. Run the command pip install -r requirements.txt
9. Run the command python3 ./firepwd.py -d ~/CTF/Fire
10. The flag is BTC{key3.dbPassword}
11. Copy the flag
12. Paste the flag into the Flag field.
13. Click Submit.

<img src="{{ 'assets/img/2022-09-01-btc-2022-last-minute-ctf-fire/btc-2022-last-minute-ctf-fire-screenshot.png' | relative_url }}" alt='BTC 2022 Last Minute CTF Fire Screenshot' />