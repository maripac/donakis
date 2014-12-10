---
layout: postInSeries
title: "004 Obtaining the Signed Certificate"
comments: true
date: 2014-11-27 14:21:16
categories: 
index: 004
short-name: "Processing the Certificate Request"
cabecera: "004 Processing the Certificate Request"
---

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

Once the certificate request has been pasted and processed, you will be provided with the final Certificate. The code should start and end as the following:

{% highlight livescript %}
-----BEGIN CERTIFICATE----- 
{% endhighlight %}

{% highlight livescript %}
-----END CERTIFICATE-----
{% endhighlight %}

Save them as /etc/ssl/certs/mailserver.pem and /etc/ssl/certs/webserver.pem. Their corresponding private keys will be in /etc/ssl/private/mailserver.pem and /etc/ssl/private/webserver.pem, respectively. You will need to copy to your local system the root certificate as well as the chain certificate file for each of these. The root certificate is commonly referenced as ca.pem while the chain certificate is by default named sub.class1.server.ca.pem. I suggest you copy them and then rename them to something like /etc/ssl/certs/mailca.pem and /etc/ssl/certs/sub.class1.mail.ca.pem, as well as /etc/ssl/certs/webca.pem and /etc/ssl/certs/sub.class1.web.ca.pem. 

The last thing we have to do, involves removing the passphrases used for the encryption of the private keys. If you take a look at the output obtained by typing the cat command followed by the path to the private keys, as in: 

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

The second and third commands will prompt you for the passphrases needed to decrypt each of the files. Once entered your decrypted private keys will be stored at the specified destination. The first command just creates the destination folder inside the certs directory that will hold the final private keys that you will need to upload to the live server.