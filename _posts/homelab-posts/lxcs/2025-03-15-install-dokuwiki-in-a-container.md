---
layout: post
title: Install Dokuwiki in a container
categories: [homelab, lxcs]
tags: [container, dokuwiki, linux, proxmox]
after-content: [disclaimer-notice.html]
---

Create an Ubuntu proxmox container.

Dokuwiki's official Ubuntu installation documentation is available at [https://www.dokuwiki.org/install:ubuntu](https://www.dokuwiki.org/install:ubuntu).


~~~
apt-get update && sudo apt-get upgrade
apt-get install apache2 libapache2-mod-php php-xml
a2enmod rewrite
cd /var/www
wget https://download.dokuwiki.org/src/dokuwiki/dokuwiki-stable.tgz
tar xvf dokuwiki-stable.tgz
mv dokuwiki-*/ dokuwiki
rm dokuwiki-stable.tgz
nano /etc/apache2/sites-enabled/000*.conf
~~~

Replace
```DocumentRoot /var/www/html```
with
```DocumentRoot /var/www/html/dokuwiki```

Open /etc/apache2/apache2.conf in a text editor.
```nano /etc/apache2/apache2.conf```

For directory /var/www/ replace
```AllowOverride None```
with
```AllowOverride All```

Restart apache2
```service apache2 restart```

Change the owner and group on /var/www/html/dokuwiki.
```chown -R www-data:www-data /var/www/html/dokuwiki```

Visit http://IP-address-of-your-server/install.php to initially configure your DokuWiki.

Delete the install.php file after finished installing.
```rm /var/www/html/dokuwiki/install.php```