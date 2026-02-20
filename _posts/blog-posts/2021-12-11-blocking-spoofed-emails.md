---
layout: post
title: Blocking spoofed emails
gh-repo: 4D5A
gh-badge: [follow]
categories: [blog]
tags: [Email, RFC, DNS, SPF, Email Security]
after-content: [disclaimer-notice.html]
---

TLDR; Who is allowed to send email for your domain name? Did you know you can tell the whole world which servers are allowed to send email from your domain name (and therefore, email sent from servers which you have not authorized should be considered fraudulent and the receiving email server should block them)? Sender Policy Framework (SPF) records are a Domain Name System (DNS) text (TXT) record which allow the owner of a domain name to specify who is allowed to send email saying they are that domain name. Basically this is a way to help determine if the sender information is real or if someone setup a fraudulent email server and is using a domain name which they are not authorized to use.

Here are some rules to remember when you add SPF records for your domain name.

1. There can only be one SPF record per domain name. These are not like A records with different TTLs. There will be only one. It isn’t uncommon to see a domain name that has more than one SPF record. If that occurs, organizations that they email will periodically block their email. They often blame the recipient or ask them to “Allow List” their domain name. Allow Listing a domain name to overcome incorrectly configured DNS is a bad idea and may increase organizational risk. No, this is not my opinion. This is codified in [Request for Comments (RFC) 7208 Section 3.2](https://tools.ietf.org/html/rfc7208#section-3.2). If you have more than one, you are violating the RFC and people who perform SPF verification on inbound email will probably block emails sent to them from your domain name because when their anti-spam system does the SPF verification the result includes multiple SPF records. What happens if you have multiple SPF records? If the recipient’s anti-spam system complies with the RFCs, the email will be blocked. The RFC detailing what should be done if multiple SPF records are received is [RFC 7208 Section 4.5](https://tools.ietf.org/html/rfc7208#section-4.5).

2. If a server should be allowed to send email from your domain name, it needs to be in your SPF record.


3. Keep your SPF record simple. If a server does not need to send email from your domain name, do not include it in your SPF record.

4. Keep include statements to a minimum. You should have fewer than 10 mechanisms (A, MX, PTR, IP4, IP6, Exists). Don’t forget if you use a mechanism, nested mechanisms count towards the limit of 10 so if you use include:spf.example.com and spf.example.com has an SPF record that contains include:spf.example2.com you are using two mechanisms. The RFC for this is [RFC 7208 Section 5](https://tools.ietf.org/html/rfc7208#section-5).

5. Please, for the love of SPF don’t use “~all”. I will write a section just about why you shouldn’t use “~all”. If you use “~all” and want to know why you should use “-all” instead or you have no clue what this means, please read the section on this.

## Performing Sender Policy Framework verification on inbound email

When your anti-spam system receives an email you want it to verify the email was sent from a server which is authorized to use the domain name the email says it came from. Why? Because if a malicious actor built an email server and told it to use your company’s domain name and you received emails and phone calls from customers saying they received spam from you that would not be good. What is worse is when malicious actors do this and instruct your customer to install malware on their computers and the emails say they are from YOU! Did you send it? No. Does your recipient know that? If you properly configured SPF and the recipient performs SPF verification, the recipient’s anti-spam system should have blocked the email. Remember, I mentioned you should not Allow List domain names to address improperly configured DNS? Suppose that you perform SPF verification for inbound emails, but someone in the organization instructs you to Allow List a domain name because, “Johnny’s email to me was blocked.” So you Allow List the domain name for Johnny’s company in your anti-spam solution. At this point, if an attacker spoofs Johnny’s company email address and sends fraudulent emails to you, your SPF verification will not stop the email. This is why you should exercise an extreme level of caution with adding domain names (or email addresses) to an Allow List.

Clearly you want to perform SPF verification inbound emails. Many widely used anti-spam solutions support performing SPF verification on inbound emails. You may have the following settings you can chose:

- Block Hard Fail / Allow Soft Fail
- Block Hard Fail / Quarantine Soft Fail
- Block Hard Fail / Block Soft Fail
- Allow Hard Fail / Allow Soft Fail

A Hard Fail is where the sender’s SPF record ends in “-all” and the email was sent from a server which is not in the SPF record.

A Soft Fail is where the sender’s SPF record ends in “~all” and the email was sent from a server which is not in the SPF record. Again, please see the section on why you shouldn’t use “~all” in your SPF record.

Some organizations choose Block Hard Fail / Allow Soft Fail. Again, please see the section on why you shouldn’t use “~” in your SPF record and also why you should treat a “~all” like a “-all” for email sent from other domain names.

## Why you shouldn’t use ~ in your SPF records

At the end of an SPF record you specify either:

- ?all
- ~all
- -all

?all is generally used to test SPF. ~all tells a recipient’s anti-spam solution, if the email was sent from a server not listed in the SPF record, then it might be real or it might be fraudulent (yeah those answers are opposites and the only options in a situation of competing hypothesis). When an anti-spam systems receives an email, does the SPF verification, the SPF records ends in “~all”, and the email was sent from a server not listed in the SPF record, the result is the verification is a Soft Fail. Since that really doesn’t tell the anti-spam solution anything why does it exist? In RFC 7028 (have you given up and read the whole thing yet? If not, I will mention it a few more times for you), it describes a Soft Fail as a result which means the email failed the verification process and perhaps you should subject the email to additional tests, but you should not block the email for this reason alone. The RFC that explains this is [RFC 7208 Section 8.5](https://tools.ietf.org/html/rfc7208#section-8.5). In my humble non IEFT opinion, this is a bad idea. If the email is fraudulent, you don’t want users clicking it. With many anti-spam solutions I have administered, there are “additional tests” which can be applied to an email which has a Soft Fail is to quarantine the email — which again, in my opinion isn’t a test. By quarantining the email, you still give the user the chance to open the email. I will write about DKIM and DMARC in another post, but I do not believe configuring DKIM and DMARC should excuse a person or group from using “-all” in their SPF record. Hopefully, your anti-spam solution also scans URLs and provides anti-virus scanning of email attachments too but I would prefer the user not receive the email.

What are you telling other companies when you use “~all”? I’m sure many people will say there is nothing wrong with using “~all”. Nerds can be judgmental of other nerds (I write this as someone who proudly identifies as a nerd). I admit, when I see a company using “~all” in their SPF record, I shake my head. There may be legitimate reasons to use “~all” in your SPF record, but I haven’t thought of one yet. Do you know how many servers do you have? Do you know which servers send email while using your domain name? If not, why doesn’t your IT department know? Seriously, DNS management is one of the most important things for a company. Without DNS accessing websites and your email won’t work. It is a significant part of your infrastructure.

If you don’t know all of the servers which should send email using your domain name, you should start making a list, determining if each server actually needs to send email — a security best practice is only authorized email servers should send email from your network, and hardening your email servers. After you know all of the servers which are allowed to send email from your domain name, why is it hard to use “-all” in your SPF record? When I see an SPF record that uses “-all”, it tells me (perhaps wrongly but hey it makes me feel good) the organization knows SPF is important. The idea of SPF has been around since at least 2000 [https://dmarcian.com/history-of-spf/](https://dmarcian.com/history-of-spf/) and was proposed by the IETF as a standard in 2014 [https://datatracker.ietf.org/doc/rfc7208/](https://datatracker.ietf.org/doc/rfc7208/). With SPF being around for more than 10 years, I feel everyone should be performing SPF verification on inbound email and just as important, publishing COMPLETE and ACCURATE SPF records for their own domain names. And yes, if your SPF record is complete and accurate then you should use “-all” and not “~all”. Please help the global Internet community stay safe by publishing complete and accurate SPF records for your domain names. In my opinion it is wrong to tell recipients it is their job to guess whether email using your domain name was sent from a server allowed to say it is from your domain name (how can you expect another person to know more about your email servers than you do?).