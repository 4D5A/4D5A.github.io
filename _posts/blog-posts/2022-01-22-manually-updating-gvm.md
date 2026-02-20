---
layout: post
title: Manually updating GVM
gh-repo: 4D5A
gh-badge: [follow]
categories: [blog]
tags: [Kali Linux, Cybersecurity, InfoSec]
after-content: [disclaimer-notice.html]
---
After reading [Kali, Postgres, GVM, and Troubleshooting]({{ site.baseurl }}{% post_url 2021-12-26-kali-postgres-gvm-troubleshooting %}) you may have GVM up and running on your linux machine. Next, you probably want to update the the Greenbone Community Feeds. If you previously used the Greenbone VM, you may be expecting the feeds to update automatically but if that doesn’t happen, then you will need to update them manually. According to a Greenbone Community post, the commands below can be used for GVM9+<sup>1</sup>:

~~~
/usr/sbin/greenbone-nvt-sync
/usr/sbin/greenbone-scapdata-sync
/usr/sbin/greenbone-certdata-sync
~~~

<img src="{{ 'assets/img/2022-01-22-manually-updating-gvm/greenbone-nvt-sync-permission-denied.png' | relative_url }}" alt='Cannot create /var/lib/openvas/feed-update.lock: Permission denied' />

If you run /usr/sbin/greenbone-nvt-sync and it states Permission denied, you might try to run sudo /usr/sbin/greenbone-nvt-sync, but if you do that Greenbone will use the account running sudo to download the new NVT information instead of the gvm user.

<img src="{{ 'assets/img/2022-01-22-manually-updating-gvm/greenbone-nvt-sync-must-not-be-executed-as-privileged-user-root.png' | relative_url }}" alt='/usr/bin/greenbone-nvt-sync must not be executed as priviledged user root' />

To properly run greenbone-nvt-sync, this command should be used:

~~~
sudo runuser -u _gvm -- greenbone-nvt-sync
~~~

If that command works, then we can use the same syntax to run the other gvm feed update commands. These are the commands you might run:

~~~
sudo runuser -u _gvm -- greenbone-nvt-sync
sudo runuser -u _gvm -- greenbone-scapdata-sync
sudo runuser -u _gvm -- greenbone-certdata-sync
~~~

The GVM Feeds should be updated once those commands are done executing.

[1] [https://community.greenbone.net/t/how-to-update-keep-the-feed-up-to-date/1431/4](https://community.greenbone.net/t/how-to-update-keep-the-feed-up-to-date/1431/4)