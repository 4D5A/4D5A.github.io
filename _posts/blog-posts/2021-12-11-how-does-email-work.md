---
layout: post
title: How does email work
gh-repo: 4D5A
gh-badge: [follow]
categories: [blog]
tags: [Email, RFC, DNS]
after-content: [disclaimer-notice.html]
---

Almost everyone uses email daily, but have you ever asked how your email reaches another person?

When you send an email, a lookup is performed to determine which server email with a specific domain name should be sent to.

The system that responds to requests for lookups is called Domain Name System (DNS).

A lookup is requested for the domain name (the part of an email address after the “@” symbol). A DNS server looks for a Mail Exchanger (MX) DNS record. The MX record tells the sender which server the email should be sent to so the domain name contained in the email address of the person to whom the email is addressed can be delivered correctly.

Because these lookups are made to servers all over the world, this information needs to be public. Here are some notional companies we will use as examples.

>_Company A_
>
>_Company B_

For a company to be able to receive email, they must publish an MX DNS record so when an email is sent to their domain name, DNS servers can inform the sending email server, the fully qualified domain name of the destination email server (i.e. what email server the email should go to). Here are the notional email servers for our notional companies. I am using “.test” as the Top Level Domain (TLD) because it is set aside by [Request for Comments (RFC) 2606](http://tools.ietf.org/html/rfc2606) to be used in documentation and is not to be used for actual networks. I am using “198.51.100.0/24” as the public IP addresses of the email servers because it is set aside by [RFC 5737](https://tools.ietf.org/html/rfc5737) to be used in documentation and are not to be used for actual networks.

>_Company A’s email server is mail.companya.test_
>_The IP address for mail.companya.test is 198.51.100.100_
>
>_Company B’s email server is mail.companyb.test_
>_The IP address for mail.companyb.test is 198.51.100.200_

Since we know the fully qualified domain names of the email servers for Company A and Company B, let’s look at what the MX records look like.

For Company A, the MX record should look like this.

~~~
companya.test MX preference=10 mail exchanger=mail.companya.test
~~~

It starts by listing the domain name for which email will be received “companya.test.” It then specifies the preference. The preference allows a company to have more than one server which can receive email. It is generally considered a good idea to have a minimum of two MX records in case the first email server is unavailable, that the email can be sent to the second email server.

For Company B, the MX record should look like this.

~~~
companyb.test MX preference=10 mail exchanger=mail.companyb.test
~~~

With the correct MX records created, we need to make sure there are DNS Forward (A) records for the fully qualified domain name, so DNS can resolve the fully qualified domain names to IP addresses.

For Company A, the A record should look like this.

~~~
mail.companya.test 198.51.100.100
~~~

For Company B, the A record should look like this.

~~~
mail.companyb.test 198.51.100.200
~~~

Bob works at Company A where his email address is bob@companyatest.

Alice works at Company B where her email address is alice@companyb.test.

When Bob who works for Company A sends an email to Alice who works at Company B an email, he enters her email address alice@companyb.test. An MX lookup is performed and Bob’s computer sends the email to mail.companyb.test.

mail.companyb.test receives the email from bob@companya.test and sees the email is addressed to alice@companyb.test. mail.companyb.test looks for the email address alice@companyb.test on its system. If mail.companyb.test finds an alice@companyb.test email address on its system, it delivers the email to it. If it does not find an alice@companyb.test email address on its system, it can do one of the following things.

1. Reject the email and do not tell Bob the email was rejected.
2. Reject the email, tell Bob the email was rejected, but not tell Bob specifically why it rejected the email.
3. Reject the email, tell Bob the email was rejected, and tell Bob the email was rejected because it did not find the address alice@companyb.example on its system.
4. Accept the email with a catch-all email account that receives emails sent to any email account for @companyb.example that does not exist.