---
layout: post
title: Unblocking yourself from WHM
gh-repo: 4D5A
gh-badge: [follow]
categories: [blog]
tags: [WHM, Linux, VPS]
after-content: [disclaimer-notice.html]
---
WHM provides some decent out of the box security tools. Those include iptables, cPHulk Brute Force Protection, and fail2ban. cPHulk and fail2ban have their own chains in iptables. If you are using both, you may end up getting locked out of WHM and not knowing how to log back in. This is more likely to happen if you only Allowlisted your public IP address in cPHulk or fail2ban and not both. cPHulk offers a web-UI and is easier for people who are new to linux to configure than fail2ban so it is understandable for someone to Allowlist their public IP address in cPHulk but not fail2ban. This may be a problem if fail2ban ever bans your public IP address as the result of a false positive (this may happen if you are logging into multiple cPanel accounts on your WHM server and there is a problem with the authentication token).S

If this occurs, your IP might only be blocked from accessing your server using the URI https://fqdn:2087 (where fqdn is your WHM server’s Fully Qualified Domain Name) but not https://whm.domainname.com (where domainname.com is your domain name). If you can login to https://whm.domainname.com you might be able to correct the problem without contacting your hosting provider.

After you login to WHM, enter “Terminal” in the search box and under “Server Configuration” click “Terminal”. Since cPHulk and fail2ban both rely on iptables, you need to remove your Blocklisted IP from both iptables chains. 

## Determine if your IP address is on the cPHulk Blocklist
To see if your IP address is in the cPHulk Blocklist, you can use the following command:

~~~
cat /usr/local/cpanel/logs/cphulkd.log | grep "IP"
~~~

## Add your IP address to the Allowlist
You will want to substitute your IP address for “IP”.<sup>1</sup> You can use the following command to Allowlist your IP from cPHulk where “IP” is the IP address you wish to Allowlist<sup>2</sup>:

~~~
/usr/local/cpanel/scripts/cphulkdwhitelist "IP"
~~~

## Determine if your IP address is on the fail2ban Blocklist
To see if your IP address is in the fail2ban Blocklist, you can use the following command:

~~~
cat /var/log/fail2ban.log | grep "IP"
~~~

## Add your IP address to the Allowlist
You will want to substitute your IP address for “IP”. You can Allowlist your IP in fail2ban by editing /etc/fail2ban/jail.conf where “IP” is the IP address you wish to Allowlist (the example assumes you want to Allowlist one IP address which is why it includes “/32”.

~~~
sudo nano /etc/fail2ban/jail.conf
ignoreip = "IP"/32
~~~

-Press CTRL + X
-Type Y
-Press Enter

Restart the fail2ban service by running the following command:

~~~
sudo service fail2ban restart
~~~

## Unban your IP address
You can use the following commands to unban your IP from fail2ban where “IP” is the IP address you wish to unban.

~~~
fail2ban-client set cpanel-iptables unbanip "IP"
fail2ban-client set recidive unbanip "IP"
~~~

## Determine if your IP address is on a seperate iptables Blocklist
To see if your IP address in in another iptables Blocklist, you can use the following command:

~~~
iptables-save | grep "IP"
~~~

## Unblock your IP address
If your IP address is in an iptables Blocklist, you need to note which iptables chain your IP address is Blocklisted in and determine the line number in the iptables chain that needs to be removed to unblock your IP address. As an example, if the iptables chain f2b-dovecot is blocking your IP address, use the following command to find the line number in the f2b-dovecot iptables chain that is blocking your IP address:

~~~
iptables -L f2b-dovecot -v -n --line-numbers
~~~

If line 2 in the iptables chain f2b-dovecot is causing your IP address to be blocked, you can use the following command to unblock your IP address:

~~~
iptables -L f2b-dovecot 2
~~~

After you remove the line in the iptables chain that was blocking your IP address, you can save the updated iptables rules with the following command:

~~~
iptables-save
~~~

[1] [https://docs.cpanel.net/knowledge-base/security/cphulk-management-on-the-command-line/](https://docs.cpanel.net/knowledge-base/security/cphulk-management-on-the-command-line/)

[2] [https://docs.cpanel.net/knowledge-base/security/cphulk-management-on-the-command-line/](https://docs.cpanel.net/knowledge-base/security/cphulk-management-on-the-command-line/)