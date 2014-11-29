---
layout: post_in_series
title: "002 Private Key and Certificate Request"
comments: true
date: 2014-11-27 16:21:16
categories: 
index: 002
short-name: "Private key and certificate request"
cabecera: "002 Private Key and Certificate Request"
---

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
