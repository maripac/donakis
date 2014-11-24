---
layout: post
title:  "Start from scratch: mailserver setup"
date:   2014-11-21 19:21:16
comments: true
categories: series mailserver
---

I am writting this post in order to document the initial setup that I performed in the production environment where this site is hosted. The purpose that I had in mind when I decided to setup the whole environment from scratch was just learning, as well as seing by first hand what the other side looks like. I have no knowledge at all on system administration, or network security. I did some research in order to find good documentation. In my opinion, as well as those that I have read in different forums the [ISPmail tutorials series](https://workaround.org/ispmail) is one of the most reliable and complete collection of documents on mailserver setup, that you can find on the internet &mdash;and it also includes some tips and recommendation on mailserver administration and maintenance&mdash;. 

It is not difficult to find other tutorials on the internet that adapt the ISPmail guide to other type of environments, as this [tutorial](https://www.digitalocean.com/community/tutorials/how-to-configure-a-mail-server-using-postfix-dovecot-mysql-and-spamassasin) for DigitalOcean droplets running Ubuntu. 

[Linode](https://www.linode.com/) has a great collection of guides and tutorials, very well designed and complete. This [guide](https://www.linode.com/docs/email/email-with-postfix-dovecot-and-mysql) was very helpfull, but I used it as a supporting guide for the ISPmail one, because it amplifies some concepts and explains a bit more in deep some information in a way that for someone with a lower level of experience like me, results easier to understand. At some point, it's important to notice that these two guides differ in the steps taken to approach the configuration of the mailserver. In my case I must say that the approach that worked out first was the one recommended in the ISPmail guide, so I did not experiment further with the configuration explained in the Linode guide. 

Anyway, no matter how well you prepare and do your research before you start the type of setup a mailserver requires, there are many chances that you will have to take your time untill all the pieces work togheter. And that has not really much to do with following one or another guide but mostly with those aspects of your environment, as well as the circumstancial coincidence of aspects that can be no problem at all when they exist separately, but when found togheter suddenly turn into a huge hurdle.

<blockquote><q>Pataphysics will be, above all, the science of the particular, despite the common opinion that the only science is that of the general. Pataphysics will examine the laws governing exceptions, and will explain the universe supplementary to this one.</q></blockquote>

In the following section of this document I will just go through the steps as they were performed in my system, mostly as a precautionary measure that will serve as a record, in case of future problems. In addition, I will provide some step by step instructions of some aspects that I found a bit vaguely described.


###Options for enabling secure connections: The TSL standard

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

###Steps before requesting a free Certificate from StartCom

Once you have understood the previous points and assumed the consequences and risks that are derived from the option of requesting a free Certificate from StartCom, the next step is to create your private key at your local machine.

{% highlight livescript %}
$ openssl genrsa -des3 -out /etc/ssl/private/mailserver.pem 4096
{% endhighlight %}

This command will launch the creation of a private key, that will be needed in the next step, in order to create the certificate request. It will prompt you for a passphrase that you will need to enter twice for verification. Once it has been created run the following command, which will also require the passphrase that you assigned to the private key before.

{% highlight livescript %}
$ openssl req -new -key /etc/ssl/private/mailserver.pem -out /etc/ssl/certs/mailserver.csr
{% endhighlight %}

It will additionally launch a wizard that will require you to enter information related to the site, that you want the Certificate to be extended to. 

{% highlight livescript %}
You are about to be asked to enter information that will be incorporated
into your certificate request.
What you are about to enter is what is called a Distinguished Name or a DN.
There are quite a few fields but you can leave some blank
For some fields there will be a default value,
If you enter '.', the field will be left blank.
-----
Country Name (2 letter code) [AU]: ES
State or Province Name (full name) [Some State]: Madrid
Locality Name (eg, city) []: Madrid
Organization Name (eg, company) [Internet Widgits Pty Ltd]: .
Organizational Unit Name (eg, section) []: .
Common Name (e.g. server FQDN or YOUR name) []: mail.mydomain.com
Email Address []: myactivemail@gmail.com

Please enter the following 'extra' attributes
to be sent with your certificate request
A challenge password []: .
An optional company name []: .
{% endhighlight %}

The seventh point, I'm not sure about the difference that would suppose entering one or another value. I decided to enter the same email address that I used when I registered my domain, but I'm not sure of what could happen if I had done it some other way. However, the previous one &mdash;the sixth point&mdash;, is the most important one. You will notice that the value requested is the Fully Qualified Domain Name for which you want the Certificate to be extended.

A FQDN is a Domain name that provides at least three labels concatenated with intermediate dots, to define a complete Domain Name Tree. The FQDN includes the TLD end which is the label at the most right of the domain, such as .com, .net, .uk. The second label is a descendant node of the parent zone, it works as a subdomain of the zone that is delimited by the TLD, in this case it would be mydomain. Finally for a FQDN to be validated as such you must provide the label at the outest left edge of the name that points to a specific hostname, where you will point your MX records in order to identify it as responsible for the mail accounts associated to your domain.

For this tutorial we'll be setting the domain that corresponds to the mailserver as mail.mydomain.com. And just for the sake of it I decided to get an additional Certificate for the webserver. So I repeated the previous steps, with small modifications. I set the passphrase to be the same as the previous private key, so I would't complicate things too much.

{% highlight livescript %}
$ openssl genrsa -des3 -out /etc/ssl/private/webserver.pem 4096
{% endhighlight %}

{% highlight livescript %}
$ openssl req -new -key /etc/ssl/private/webserver.pem -out /etc/ssl/certs/webserver.csr
{% endhighlight %}

{% highlight livescript %}
You are about to be asked to enter information that will be incorporated
into your certificate request.
What you are about to enter is what is called a Distinguished Name or a DN.
There are quite a few fields but you can leave some blank
For some fields there will be a default value,
If you enter '.', the field will be left blank.
-----
Country Name (2 letter code) [AU]: ES
State or Province Name (full name) [Some State]: Madrid
Locality Name (eg, city) []: Madrid
Organization Name (eg, company) [Internet Widgits Pty Ltd]: .
Organizational Unit Name (eg, section) []: .
Common Name (e.g. server FQDN or YOUR name) []: www.mydomain.com
Email Address []: myactivemail@gmail.com

Please enter the following 'extra' attributes
to be sent with your certificate request
A challenge password []: .
An optional company name []: .
{% endhighlight %}

###Creation of the StartCom user account

To carry on with the following steps you can forget about the previously created certificate requets because they will be used later. Now you need to sign in for an account at the [StartCom Certification Authority](https://www.startssl.com/). There is some aspects that is best to be informed about before signing into their website. 

StartSSL will not authenticate yourself in the normal way in which you would have a user password that you could use from any computer to sign into your account. Instead, StartSSL will create a key that will be stored in your browser once you have accepted the operation. From now on, each time you access the StartSSL website, you will be asked to allow StartSSL to access the document. This means that you must access your profile at StartSSL from the same computer and browser that you used to register the first time. Since StartSSL free certificates are extended for just one year of duration, it is very important that you burn this personal key to an external CD and keep it safely and outside your computer. This will prevent future problems whenever you need to update certificates, etc... That way you will always have the posssibility of installing your personal key in other browsers that will allow you to access the information associated to your certificates in the future.

Next you have to validate your personal email. To complete this step you can navigate to the [StartSSL main page](https://www.startssl.com/) and click on the button that looks like the one below, which allowes you to "Log into your control panel".

![login button]({{site.baseurl}}/images/auth.png "login button") 

From the control panel you first have to click on the Validation tab, and select from the dropdown menu the email validation option. Next follow steps in order to validate your email account. 

###Domain Name validation 

At this point we will proceed with the Domain Name validation, that as explained before will prove yourself as the owner of the Domain Name for which you want to request a certificate. Navigate to the [StartSSL main page](https://www.startssl.com/) and again click the button that will allow you to enter the control panel. Inside the control panel you must click again on the Validation tab, but unlike the previous step, now we must choose from the dropdown menu the option that corresponds to the Domain Name Validation.

<img src="{{site.baseurl}}/images/validation.png" alt="Domain Name Validation" style="border:1px solid #003300;"/>

Then you will enter the Domain Name for which you want to prove ownership. It is not needed that you enter here a FQDN, so enter just the Domain Name as it was registered (e.g, mydomain.com). At this point the StartSSL engine should have performed a whois query on your domain, the one that you have configured with your DNS provider to be included within your whois contact info. Select it, and go to your email account to retrieve the needed verification code, that StartSSL has sent to you. Checkout the spam mailbox, in case you do not find it or click the send new verification code option in the StartSSL graphical interface.

Once you have made yourself with the verification code sent to the email account associated to the Domain, go back to StartSSL and paste it inside the text field that has been enabled for that purpose. StartSSL should respond with the corresponding feedback that will let you know wether you have successfully validated the Domain Name or not. 

###Certificates request

Now it's time to check for the availability of the certificate requests &mdash;the .csr files&mdash; that were previously generated in our local system. We are going to request two different certificates: one for www.mydomain.com, and another for the mail.mydomain.com. We should be able to retrieve each of those by typing the following commands from the terminal:

In the case of the mail.mydomain.com FQDN:

{% highlight livescript %}
$ cat /etc/ssl/certs/mailserver.csr
{% endhighlight %}

In the case of the the www.mydomain.com FQDN:

{% highlight livescript %}
$ cat /etc/ssl/certs/webserver.csr
{% endhighlight %}

Both should respond with a very long output of letters and numbers that must start and end as:

{% highlight livescript %}
-----BEGIN NEW CERTIFICATE REQUEST----- 
{% endhighlight %}

{% highlight livescript %}
-----END NEW CERTIFICATE REQUEST-----
{% endhighlight %}

So, as the previous output states what we have done so far is creating a public certificate request from a private key. Now we need to apply the formalization of this request to the StartCom Certification Authority, in order to satisfy this application with its corresponding Certificate file.

Navigate to the [StartSSL main page](https://www.startssl.com/), enter the control panel, and click on the Certificates tab. From the dropdown menu select the option as displayed below:

<img src="{{site.baseurl}}/images/cert-wizard.png" alt="Certification Wizard" style="border:1px solid #003300;"/>

Click to continue. Now skip the creation of any private keys, because we have already created them locally. At some point you will need to select from the validated list of Domain Names the correct one, and enter a subdomain (either www or mail). Next you must enter the certificate request that corresponds to the subdomain that you are currently applying to. This means that the csr that must be pasted when we request the Certificate for www.mydomain.com is the one that is outputed from the following command:

{% highlight livescript %}
$ cat /etc/ssl/certs/webserver.csr
{% endhighlight %}

In the same way, when it comes to the creation of the Certificate that will be assigned to the subdomain mail.mydomain.com, we must remember to enter the output returned by running the following:

{% highlight livescript %}
$ cat /etc/ssl/certs/mailserver.csr
{% endhighlight %}

Once the certificate request has been pasted and processed, you will be provided with a certificate key that StartSSL will recommend you to copy and save in a file called ssl.crt. The code should start and end as the following:

{% highlight livescript %}
-----BEGIN CERTIFICATE----- 
{% endhighlight %}

{% highlight livescript %}
-----END CERTIFICATE-----
{% endhighlight %}

I prefer to save them as /etc/ssl/cert/mailserver.pem and /etc/ssl/certs/webserver.pem. Their corresponding private keys will be in /etc/ssl/private/mailserver.pem and /etc/ssl/private/webserver.pem, respectively. You will need to copy to your local system the root certificate as well as the chain certificate file for each of these. The root certificate is commonly referenced as ca.pem while the chain certificate is by default named sub.class1.server.ca.pem. I suggest you copy them and then rename them to something like /etc/ssl/certs/mailca.pem and /etc/ssl/certs/sub.class1.mail.ca.pem, as well as /etc/ssl/certs/webca.pem and /etc/ssl/certs/sub.class1.web.ca.pem. 

The last thing we have to do, involves the deleting the passphrases used for the encryption of the private keys. If you take a look at the output obtained by typing the cat command followed by the path to the private keys, as in: 

{% highlight livescript %}
$ cat /etc/ssl/private/mailserver.pem
{% endhighlight %}

You will get an output similar to the one below. 

{% highlight livescript %}
-----BEGIN RSA PRIVATE KEY-----
Proc-Type: 4,ENCRYPTED
{% endhighlight %}

{% highlight livescript %}
-----END RSA PRIVATE KEY-----
{% endhighlight %}

As you'll notice right after the initial definition line, there is a following line of text that identifies the following block of code as encrypted text. So before we upload those files to the live site, we should first decrypt its contents.

{% highlight livescript %}
$ mkdir /etc/ssl/certs/private
$ openssl rsa -in /etc/ssl/private/mailserver.pem -out /etc/ssl/certs/private/mailserver.pem
$ openssl rsa -in /etc/ssl/private/webserver.pem -out /etc/ssl/certs/private/webserver.pem
{% endhighlight %}

The seond and third commands will prompt you for the passphrases needed to decrypt each of the files. Once entered your decrypted private keys will be stored at the specified destination. The first command just creates the destination folder inside the certs directory that will hold the final private keys that you will need to upload to the live server.


###How do theese files work togheter...and for how long?

A digital certificate certifies the ownership of a public key by the named subject of the certificate. This allows others (relying parties) to rely upon signatures or on assertions made by the private key that corresponds to the certified public key. In this model of trust relationships, a CA is a trusted third party - trusted both by the subject (owner) of the certificate and by the party relying upon the certificate. 

It will not be very difficult to bear the possibility of some type of collapse concerning this whole structure of trust that might affect the current system of CA (Certificate Authorities) in a short time basis. The chain certificate structure is based on a layered system, whose logic operates mainly by assuming as trustworthy those layers that have been denoted as such by an entity that is located before another in a chained structure. This adds up for a very delicate balance, which is already starting to raise a number of doubts around its own strenght. So, in my personal opinion I would not underestimate the new emerging proposals such as [Let's Encrypt](https://letsencrypt.org/) that hopefully will turn into a reality those better perspectives that will bring to an end the type boundaries that surprisingly enough still make of the internet a place of unequal access to permanent resources. 

<!--![Domain Name Validation]({{site.baseurl}}/images/validation.png "Domain Name Validation") -->

<!--A FQDN is a Domain name that provides at least three labels &mdash;concatenated with intermediate dots&mdash; that define a complete Domain Name Tree, so that the sole concatenation of these three labels in the order that the Domain Name System states as part of their policy, it is possible to define the exact delimitation of a given Zone of Authority that is associated to any Domain Name that exists within the whole address space that constitutes the Domain Name Hierarchical System. Each of these labels denote a single zone located inside of an ancestor zone. The DNS hierarchy is similar to the hierarchy of folders inside a unix file system, but with the major exception that when defining a DNS zone, the descendant zones are located at the left side of the ancestor labels for those zones.


   delimitation of the boundaries of its own authoritative zone within the hierarchical definition of zones that structure the Domain Name Address Space. defining each of those labels a zone descending from the one at its most direct right, in the Domain Name Hierarchy. In a summarized way, to define a FQDN we should provide at least three labels. The outest right label (such as .com, .net, etc...) is known as the TLD for Top Level Domain, the next one (mydomain, example,, etc...) is a subdomain zone that descends from the .com or .net zone, and exists within the authoritative region that responds for the .com or .net regions.


 of domain name  a TLD 'Top-Level Domain' such as 'com', 'net' and such. The actual domain label such as 'mydomain', which is no more than a subdomain of the 'com' TLD in the hierarchy of domains

which is usually the SLD 'Second-Level Domain' which is the -->


<!--In case none of the previous worked, maybe you can update the email account associated to your domain by editing the SOA record. The SOA record, does not have to be managed exclusively within your DNS provider. -->

<!--DNS providers, usually point your purchased domain to their own Name Server. That will be the Authoritative Name Servers responsible for your domain. Name Servers are servers in charge of providing an associated IP in response to queries requesting a domain. Name Servers are nodes of the Domain Name System database, the DNS is therefore a distributed database, whose data is delegated trought a network constituted of Name Servers. Name Servers can act as Primary Name Srvers, which are those that contain the portion of the DNS that affect the domains contained inside the zone for which they are responsible of.   

However, it is very common that instead of the previous configuration, you prefer that this authoritative Name Server acted as a Secondary Name Server (slave) and responded with the updated records that you define at some other Name Server that you design as Primary (master). -->   
