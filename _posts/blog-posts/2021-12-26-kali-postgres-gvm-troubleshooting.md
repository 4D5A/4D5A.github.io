---
layout: post
title: Kali, Postgres, GVM, and Troubleshooting
gh-repo: 4D5A
gh-badge: [follow]
categories: [blog]
tags: [Kali Linux, Cybersecurity, InfoSec]
after-content: [disclaimer-notice.html]
---

Everything was working well, that is until you decided to install a newer version of Postgres, right?

## Configuring Postgres

First, you need to make sure that your new version of Postgres is running on port 5432.<sup>1</sup>

For example, if the new version of Postgres is version 14, then you would need to open /etc/postgres/14/main/postgresql.conf and see what port it is using.<sup>2</sup>

If it is not using port 5432, then you will need to either change the port numbers your other versions of Postgres are using so you can configure the new version of Postgres to use port 5432, or you will need to uninstall the other versions of Postgres (which may break other programs unless you make sure they are all using the new version of Postgres and upgrade their Postgres clusters as well.<sup>3</sup>

Once you have the new version of Postgres listening on port 5432, you need to upgrade the GVM Postgres cluster from the old version of Postgres to the new version of Postgres.

In the following example, I assume that you have just installed Postgres version 14, that you have restarted your computer, and that you do not have any data being stored in any Postgres version 14 databases, because there aren’t any yet. If you already have data stored in any Postgres version 14 databases, then don’t drop the cluster unless you are willing to loose all of the data stored there.

For example, if the old version of Postgres is version 13 and the new version of Postgres is version 14, you would run the following commands:<sup>4</sup>

First we should stop the Postgres version 14 cluster. We need to do this, because we need to upgrade the Postgres version 13 cluster to Postgres version 14 but when we installed Postgres version 14, it created a cluster that is not being used and if we leave it there, will cause the upgrade of the cluster from the older version of Postgres to fail.

~~~
sudo pg_dropcluster --stop 14 main
~~~

Now we can upgrade the Postgres version 13 cluster.

~~~
sudo pg_upgradecluster 13 main
~~~

Next, you should restart your Postgres service. To restart the Postgres service, you can run this command:

~~~
sudo systemctl restart postgresql
~~~

## Installing GVM

If you do not already have GVM installed, you can do that now. This assumes you have already checked for kali updates, installed them, and restarted your computer. You will want to run the following command:

~~~
sudo apt install gvm
~~~

After you install GVM, you need to run the Kali GVM setup script. Please notice, gvm-setup is a Kali maintained script and is not created, maintained, or supported by Greenbone.<sup>5</sup>

## Using gvm-setup

If you experience a problem running gvm-setup, you can try these steps or search Kali’s forums [https://forums.kali.org/](https://forums.kali.org/). While the Greenbone staff are very helpful and often reply to questions on the Greenbone forums, they did not create, do not maintain, and do not support the gvm-setup script. The gvm-setup script was created by and is maintained by Kali.

If you run gvm-setup and it tells you that it cannot connect to the database, stop the process because the setup is not going to store the information in Postgres. You need to fix the Postgres problem(s) first and then re-run gvm-setup.

## If you can’t login to GVM

If you are having a problem logging into GVM, then first see if the account is stored in the Postgres database _gvm with this command<sup>6</sup>:

~~~
sudo runuser -u _gvm -- gvmd --get-users
~~~

If you see your account but you cannot login, you can run this command to reset the password<sup>7</sup>:

~~~
sudo runuser -u _gvm -- gvmd --user=admin --new-password=NEWPASSWORD
~~~

If you cannot login after you reset the password, you can try restarting the gvm service by running the following command:

~~~
sudo systemctl restart gvmd
~~~

You can also create a new GVM user but running the following command<sup>8</sup>:

~~~
sudo runuser -u _gvm -- gvmd --create-user=USER --new-password=NEWPASSWORD
~~~

To delete a GVM user, you can run the following command:

~~~
sudo runuser -u _gvm -- gvmd --delete-user=USER
~~~

[1] [https://community.greenbone.net/t/gvm-install-setting-on-kali-linux-2020-3/7298/6](https://community.greenbone.net/t/gvm-install-setting-on-kali-linux-2020-3/7298/6)

[2] [https://forums.kali.org/showthread.php?50523-GVM-installation-problems&p=107032#post107032](https://forums.kali.org/showthread.php?50523-GVM-installation-problems&p=107032#post107032)

[3] [https://forums.kali.org/showthread.php?50523-GVM-installation-problems&p=107032#post107032](https://forums.kali.org/showthread.php?50523-GVM-installation-problems&p=107032#post107032)

[4] [https://bugs.kali.org/view.php?id=6764](https://bugs.kali.org/view.php?id=6764)

[5] [https://community.greenbone.net/t/gvm-install-setting-on-kali-linux-2020-3/7298/5](https://community.greenbone.net/t/gvm-install-setting-on-kali-linux-2020-3/7298/5)

[6] [https://community.greenbone.net/t/cant-login-to-openvas-web/9397/3](https://community.greenbone.net/t/cant-login-to-openvas-web/9397/3)

[7] [https://community.greenbone.net/t/cant-login-to-openvas-web/9397/3](https://community.greenbone.net/t/cant-login-to-openvas-web/9397/3)

[8] [https://community.greenbone.net/t/greenbone-security-assistant-20-08-1-admin-password-reset/8544](https://community.greenbone.net/t/greenbone-security-assistant-20-08-1-admin-password-reset/8544)

