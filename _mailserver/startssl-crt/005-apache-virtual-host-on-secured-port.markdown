---
layout: postInSeries
title: "005 Apache VirtualHost configuration"
comments: true
date: 2014-11-27 01:21:16
categories: 
index: 005
short-name: "Apache configuration"
cabecera: "005 Apache VirtualHost configuration"
---

After you finish the whole process. You should have the following files.

+	`/etc/ssl/certs/private/webserver.pem`
+	`/etc/ssl/certs/webserver.pem`
+	`/etc/ssl/certs/sub.class1.web.ca.pem`
+	`/etc/ssl/certs/webca.pem`

Neither the encrypted private key, nor the request .csr file are needed anymore. The root certificate, is not going to be needed for the Apache VirtualHost setup. But keep it near because it will be needed later. Now you could scp them to your vps, and store them maintaining the same structure of directories. 

To scp files from local to a remote server, the destination folder must exist in the remote machine. One way to do this is by cd'ing to your local directory /etc/ssl/certs/private/ and ssh'ing to your vps to create the destination folder. 

{% highlight livescript %}
$ cd /etc/ssl/certs/private
$ ssh user@mydomain
$ mkdir /etc/ssl/certs/private
{% endhighlight %}

Logout, and back at your local system upload the decrypted private key:

{% highlight livescript %}
$ scp webserver.pem user@mydomain:/etc/ssl/certs/private/webserver.pem 
{% endhighlight %}

Move to the parent directory to upload the Certificate file, along with the Intermediate CA Certificate, and the Root CA Certificate:

{% highlight livescript %}
$ cd .. 
$ scp webserver.pem user@mydomain:/etc/ssl/certs/webserver.pem
$ scp sub.class1.web.ca.pem user@mydomain:/etc/ssl/certs/sub.class1.web.ca.pem
$ scp webca.pem user@mydomain:/etc/ssl/certs/webca.pem
{% endhighlight %}
<br>

###The first part of this section applies just in a case where you only had one TLS certificate per IP. 

######You can jump to the [next option](#sni) if that is not your case.

In normal circumstances, now you could login to your vps and open with a text editor the template file that is used for enabling an apache VirtualHost on port 443, which is the port employed for establishing encrypted connections: 

{% highlight livescript %}
$ sudo nano /etc/apache2/sites-available/default-ssl
{% endhighlight %}

And update the path to the Private Key, the Certificate File, as well as the Intermediate CA Certificate as follows:

{% highlight apacheconf %}
<IfModule mod_ssl.c>
<VirtualHost _default_:443>
        ServerAdmin webmaster@localhost
        ServerName www.mydomain.com  
        DocumentRoot /var/www/html

        ErrorLog ${APACHE_LOG_DIR}/error.log

        # Possible values include: debug, info, notice, warn, error, crit,
        # alert, emerg.
        LogLevel warn

        CustomLog ${APACHE_LOG_DIR}/ssl_access.log combined

        #   SSL Engine Switch:
        SSLEngine on

        SSLProtocol all -SSLv2
        SSLCipherSuite ALL:!ADH:!EXPORT:!SSLv2:RC4+RSA:+HIGH:+MEDIUM

        SSLCertificateFile    /etc/ssl/certs/webserver.pem
        SSLCertificateKeyFile /etc/ssl/certs/private/webserver.pem
        SSLCertificateChainFile /etc/ssl/certs/sub.class1.web.ca.pem
</VirtualHost>
</IfModule>
{% endhighlight %}

To enable this SSL configuration, as well as the ssl module, and reload apache to update the new configuration. 

{% highlight livescript %}
$ a2ensite default-ssl
$ a2enmod ssl
$ service apache2 reload
{% endhighlight %}

At this point, if you haven't decrypted the private key, you might get an error. To make sure, the Private Key is properly encoded also restart:

{% highlight livescript %}
$ service apache2 restart
{% endhighlight %}

Now you should be able to get an It Works! message if you navigate to https://www.mydomain.com. And finally edit:

{% highlight livescript %}
$ sudo nano /etc/apache2/sites-available/default
{% endhighlight %} 

To add a redirection statement:

{% highlight apacheconf %}
Redirect permanent / https://www.mydomain.com/
{% endhighlight %}

To get apache to apply changes:

{% highlight livescript %}
$ service apache2 reload
{% endhighlight %}

<a name="sni"></a>

###In order to use more than one TLS certificate per IP, we must enable an extention of the TLS protocol that is known as Server Name Indication or SNI. 

You can read more about this implementation of the TLS protocol on the following links:

+ [RFC 4366](http://tools.ietf.org/html/rfc4366)
+ [Apache Documentation NameBasedSSLVHostsWithSNI](https://wiki.apache.org/httpd/NameBasedSSLVHostsWithSNI)

In order to configure a second VirtualHost on a 443 port, you should scp the other group of files corresponding to the other TLS connection that you want to enable. In my case: 

+	`/etc/ssl/certs/private/mailserver.pem`
+	`/etc/ssl/certs/mailserver.pem`
+	`/etc/ssl/certs/sub.class1.mail.ca.pem`
+	`/etc/ssl/certs/mailca.pem`

You can also check out the following tutorial for more details on this configuration:

[Multiple SSL Certificates on one IP](https://www.digitalocean.com/community/tutorials/how-to-set-up-multiple-ssl-certificates-on-one-ip-with-apache-on-ubuntu-12-04)

The idea is to create two files in `/etc/apache2/sites-available`. One for each of the Name Based Virtual Host, that you want to provide secure access to. So to keep it intuitive you could create a file at the given location called `www.mydomain.com`, while the other could be `mail.mydomain.com`. 

Obviously before this whole setup is publicly available from a web client you need to configure the DNS properly. The next file is the one that creates the webserver VirtualHost, called `www.mydomain.com`.  

{% highlight apacheconf %}

<VirtualHost *:80>
        ServerAdmin webmaster@localhost
        ServerName www.mydomain.com  
        DocumentRoot /var/www/html

        ErrorLog ${APACHE_LOG_DIR}/error.log
        Redirect permanent / https://www.mydomain.com/

        # Possible values include: debug, info, notice, warn, error, crit,
        # alert, emerg.
        LogLevel warn

        CustomLog ${APACHE_LOG_DIR}/access.log combined
</VirtualHost>

<IfModule mod_ssl.c>
<VirtualHost *:443>
        ServerAdmin webmaster@localhost
        ServerName www.mydomain.com  
        DocumentRoot /var/www/html

        ErrorLog ${APACHE_LOG_DIR}/error.log

        # Possible values include: debug, info, notice, warn, error, crit,
        # alert, emerg.
        LogLevel warn

        CustomLog ${APACHE_LOG_DIR}/ssl_access.log combined

        # SSL Engine Switch:
        SSLEngine on

        SSLProtocol all -SSLv2
        SSLCipherSuite ALL:!ADH:!EXPORT:!SSLv2:RC4+RSA:+HIGH:+MEDIUM


        SSLCertificateFile    /etc/ssl/certs/webserver.pem
        SSLCertificateKeyFile /etc/ssl/certs/private/webserver.pem
        SSLCertificateChainFile /etc/ssl/certs/sub.class1.web.ca.pem

        <FilesMatch "\.(cgi|shtml|phtml|php)$">
                SSLOptions +StdEnvVars
        </FilesMatch>
        <Directory /usr/lib/cgi-bin>
                SSLOptions +StdEnvVars
        </Directory>
        #   SSL Protocol Adjustments:
        #   o ssl-unclean-shutdown:
        #   o ssl-accurate-shutdown:
        BrowserMatch "MSIE [2-6]" \
                nokeepalive ssl-unclean-shutdown \
                downgrade-1.0 force-response-1.0
        # MSIE 7 and newer should be able to use keepalive
        BrowserMatch "MSIE [17-9]" ssl-unclean-shutdown

</VirtualHost>
</IfModule>

{% endhighlight %}


Next, you can create a second one called `mail.mydomain.com`, following the same parameters with the paths pointing to the right certificates. The following code, also includes the options needed to configure Roundcube, so at this point it won't work. But we'll test the TLS connection on the webserver, instead of the mailserver. 

{% highlight apacheconf %}
<VirtualHost *:80>
        ServerAdmin webmaster@localhost
        Alias /roundcube/program/js/tiny_mce/ /usr/share/tinymce/www/
        ServerName mail.mydomain.com
        DocumentRoot /var/lib/roundcube

        ErrorLog ${APACHE_LOG_DIR}/error.log

        Redirect permanent / https://mail.mydomain.com/

        # Possible values include: debug, info, notice, warn, error, crit,
        # alert, emerg.
        LogLevel warn

        CustomLog ${APACHE_LOG_DIR}/access.log combined
</VirtualHost>

<IfModule mod_ssl.c>
<VirtualHost *:443>
        ServerAdmin webmaster@localhost
        Alias /roundcube/program/js/tiny_mce/ /usr/share/tinymce/www/
        ServerName mail.mydomain.com
        DocumentRoot /var/lib/roundcube

        ErrorLog ${APACHE_LOG_DIR}/error.log

        # Possible values include: debug, info, notice, warn, error, crit,
        # alert, emerg.
        LogLevel warn


        CustomLog ${APACHE_LOG_DIR}/ssl_access.log combined

        #   SSL Engine Switch:
        #   Enable/Disable SSL for this virtual host.
        SSLEngine on

        SSLProtocol all -SSLv2
        SSLCipherSuite ALL:!ADH:!EXPORT:!SSLv2:RC4+RSA:+HIGH:+MEDIUM

        SSLCertificateFile    /etc/ssl/certs/mailserver.pem
        SSLCertificateKeyFile /etc/ssl/certs/private/mailserver.pem
        SSLCertificateChainFile /etc/ssl/certs/sub.class1.mail.ca.pem

       <FilesMatch "\.(cgi|shtml|phtml|php)$">
                SSLOptions +StdEnvVars
        </FilesMatch>
        <Directory /usr/lib/cgi-bin>
                SSLOptions +StdEnvVars
        </Directory>

        BrowserMatch "MSIE [2-6]" \   
                nokeepalive ssl-unclean-shutdown \
                downgrade-1.0 force-response-1.0
        # MSIE 7 and newer should be able to use keepalive
        BrowserMatch "MSIE [17-9]" ssl-unclean-shutdown

</VirtualHost>
</IfModule>

{% endhighlight %}


Then, edit the ports.conf file 

{% highlight livescript %}
$ sudo nano /etc/apache2/ports.conf 
{% endhighlight %}

Leave it as the code below:

{% highlight apacheconf %}
NameVirtualHost *:80
NameVirtualHost *:443

Listen 80

<IfModule mod_ssl.c>
    Listen 443
</IfModule>

<IfModule mod_gnutls.c>
    Listen 443
</IfModule>
{% endhighlight %}


Maybe you need to disable previous configurations, before enabling the one we just setup:

{% highlight livescript %}
$ sudo a2dissite default
$ sudo a2dissite default-ssl
{% endhighlight %}

And enabling the new VirtualHosts

{% highlight livescript %}
$ sudo a2ensite www.mydomain.com
$ sudo a2ensite mail.mydomain.com
{% endhighlight %}

In order for Apache to update the new settings:

{% highlight livescript %}
$ sudo service apache2 reload
$ sudo service apache2 restart
{% endhighlight %}

We can test the webserver connection by typing in a web browser www.mydomain.com. In relation to the DNS settings that must be assigned for the previous to work, it just consists of an A record that points the domain root, without any preffix to the public IP, as well as a CNAME record that refers the www. preffixed domain to the domain name as in the following rules. But remember that these records might need up to 24 hours to fully propagate:

{% highlight livescript %}
mydomain.com. 1800 IN A 111.22.333.444
www.mydomain.com. 1800 IN CNAME mydomain.com. 
{% endhighlight %}

If everything went OK the browser should automatically be redirected to https://www.mydomain.com. The lock at the left of the navigation bar should be green, and the browser should not warn of unsecure connections.




