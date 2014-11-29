---
layout: post_in_series
title: "001 Options for enabling secure connections: The TLS protocol"
comments: true
date: 2014-11-27 19:21:16
categories: 
index: 001
short-name: "Options for enabling TLS"
cabecera: "001 Options for enabling secure connections: The TLS protocol"
---

As explained in the ISPmail guide, the first steps to be taken involve [making your mailserver capable of establishing TSL connections](https://workaround.org/ispmail/wheezy/tlsifying-your-server). There are four different ways in which you can approach this requirement.

1. Without any type of certificate. 
:	In which case every time you establish a connection to the mailserver you'll have to tell the client that you trust the server.

2. By creating your own certificate. 
:	In which case you'll have to configure a security exception once for every client you use to connect to your mailserver, so that the client knows that you trust the server.

3. By requesting a free certificate.
:	From the [StartCom Certification Authority](https://www.startssl.com/).

4. By paying for a certificate.
:	From any other Certification Authority.


I chose to take the third option and proceeded with the request of a free cretificate. There are a couple of things to take into account surrounding the [StartCom policy](http://en.wikipedia.org/wiki/StartCom#Limitations_of_StartSSL_Free), the one that has caused a lot of debate regards their [response to the heartbleed vulnerability](http://en.wikipedia.org/wiki/StartCom#Response_to_heartbleed). You can read more on this debate [here](https://news.ycombinator.com/item?id=7557764), and [here](https://bugs.debian.org/cgi-bin/bugreport.cgi?bug=744027).

It is true that I did not have the information provided above when I made my decision, but I must say I did not either take the time to search around, so my fault, obviously. Anyway, as it is stated in some of the links above there are two things to be precautious about surrounding the StartCom Authority:

+	Revocation has a fee of 24.90$.
+	The validation requires a working mail account associated to the domain.

The first point, means that to avoid paying a fee of 24.90$, the FQDN, Fully Qualified Domain Name, that you want the certificate to be extended for, must match:

+	The FQDN that you enter when you create the requesting document in your local system.
+	The FQDN that you enter in the StartSSL website when requesting the certificate.
+	The FQDN that you assign to your mailserver whith your DNS manager.

The second point, means that, before you can request a certificate for a domain at StartCom, first you will have to prove yourself as the owner of the domain in question. The way in which StartCom proceeds with this validation is by sending a mail containing a password to a mail account such as webmaster@yourdomain.com, info@yourdomain.com, hostmaster@yourdomain.com and such. Since you are setting up a mail server, chances are that you will not have access to those email accounts. In that case you have to make sure that you own the domain, and that it is registered in the way that I will explain.

When you register a domain with a DNS provider, you normaly create a user account that is associated with your current mail account. In normal circumstances, after you have purchased a domain your DNS provider creates a `whois` record and publishes it on the internet. Obviously if you have purchased your domain and enabled some kind of privacy protection feature, your whois information will not be publicly available. In addition, there are some DNS providers that do not include your email account within the whois record. That was the case with [iwantmyname.com](https://iwantmyname.com/), and I solved it in a way that does not apply here. So what I would recommend is first of all, that you clear out any doubts by running `whois yourdomain.com` from the terminal, additionally this [website](http://whois.domaintools.com/) might provide a more complete report. And secondly, if the previous command did not output your working email account, try to contact directly your DNS provider to explain the situation. Also note that any update performed on the information associated to your domain will need at least 24 hours to propagate accross the internet.

