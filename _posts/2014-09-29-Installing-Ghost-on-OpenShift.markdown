---
layout: post
title:  "Installing Ghost on OpenShift"
date:   2014-09-29 02:26:00
comments: true
categories:
subtitle: " How to sync your Ghost live blog with a cloned repository that runs locally"
---
**Note** *This post is being written with the intention of keeping a personal record of the actions performed in my particular environment, meeting a set of conditions which I'm not sure I'll be able to point out in every case. Including ruby, git and node.js version; operating system and so on. OpenShift provides plenty of documentation regarding these options which I will try to refer, and which I strongly recommend to consult before trying to perform any modification in any other particular environment.* 
 
## Installation and configuration of the Client Tools
 
 
[Installing the Client Tools](https://developers.openshift.com/en/getting-started-client-tools.html)
 
[Extended documentation](https://access.redhat.com/documentation/en-US/OpenShift_Online/2.0/html/Client_Tools_Installation_Guide/index.html)
 
Once we have verified that Ruby and Git are available in our system we can install the OpenShift gem.

{% highlight livescript %}
$ sudo gem install rhc
{% endhighlight %}

Once the installation has completed, run:

{% highlight livescript %}
$ rhc setup
{% endhighlight %}    


[Configuration of the Client Tools](https://access.redhat.com/documentation/en-US/OpenShift_Online/2.0/html/Client_Tools_Installation_Guide/Configuring_Client_Tools.html)

We'll be prompted to enter our login information. First we have to enter the email account that we have previously used to sign in to the OpenShift platform.

{% highlight livescript %}
Login to openshift.redhat.com: 
{% endhighlight %}

And next our password:

{% highlight livescript %}
Login to openshift.redhat.com: email@account.com
Password:******
{% endhighlight %}
    
For details on this process please refer to the link above. In a summarized way I think I was asked if I wanted to generate a token, which I accepted by typing yes and pressing enter. Then the wizard outputs the path in you local system where the configuration file that contains your credentials, has been created. Then when your SSH keys have been created, you are prompted with this question which I accepted.

{% highlight livescript %}
Your public ssh key must be uploaded to the OpenShift server to access code.
Upload now? (yes|no) yes
{% endhighlight %}
    
And by the end of this process you are able to choose a domain.

{% highlight livescript %}
Please enter a domain (letters and numbers only) |<none>|: MyDomain
Your domain name 'MyDomain' has been successfully created
{% endhighlight %}    
    
    
## Creating the Ghost application
 
 
 * [How to install Ghost on OpenShift](http://howtoinstallghost.com/how-to-install-ghost-on-openshift/)
 
 * [Quickstart reference](https://www.openshift.com/quickstarts/ghost)
 
 * [Github repository of OpenShift's Ghost code](https://github.com/openshift-quickstart/openshift-ghost-quickstart)
 
To setup Ghost as an application on your OpenShift's account cd to the folder holding your other local projects. In my case `$ cd ~/Sites` and type:
 
 **Note** *Where ghost will be the domain that will be followed by the domain chosen when configuring the client tools, both adding to something like ghost-mydomain.rhcloud.com which is the url where you will be able to access your Ghost application. As well as the name of the folder that will be created in your local system holding a cloned repository of the public site. You can change it to anything you prefer.*
 
{% highlight livescript %}
$ rhc app create ghost nodejs-0.10 --env NODE_ENV=production --from-code https://github.com/openshift-quickstart/openshift-ghost-quickstart.git
{% endhighlight %}     


###Running the Application Locally

As explained in the README.md file of the Github repository linked above. The Ghost repository cloned to `~/Sites/ghost` is missing two folders. If we open the .gitignore file from the root folder, we see they should be inside the content folder, so we can add them to our local installation since we aren't facing the risk of overwritting any of them when pushing changes from local to the live site. So inside the content folder I added an images folder and a data folder. Then I removed the node_modules folder and added it to the beginning of the .gitignore file.

If you log in to your OpenShift account and go to applications. You must see that you have one application called ghost. If you click on the name ghost. You enter to another very similar screen. In the right sidebar highlighted with red in the image next. If we click the link "Want to log in to your application?"


![Screen Capture]({{site.baseurl}}/images/ssh-1.png "Screen Capture")


Then we can select and paste the credentials we need to connect to our hosting with ssh they should be something like 

{% highlight livescript %}
$ ssh 666ddddsgytsyvus@ghost-mydomain.rhcloud.com
{% endhighlight %} 

In order to check the version of Node.js that our server is running we can enter

{% highlight livescript %}
$ ssh 666ddddsgytsyvus@ghost-mydomain.rhcloud.com node -v
{% endhighlight %} 

That should output the node.js version. In my case the node.js version was 0.10.25. My local environment node.js version was verified by typing:

{% highlight livescript %}
$ npm -v
{% endhighlight %} 

Since I had to downgrade a version because I was running locally node v0.10.26 .I followed the next tutorial on how to install n which is an npm module for managing node.js versions. 

**Note** *I do not fancy much this step. I can't say yet if this can get me into further problems regarding my other local projects*

[Node Version Management](http://theholmesoffice.com/node-js-fundamentals-how-to-upgrade-the-node-js-version/)

Once I verified that my local node.js version was as well 0.10.25. I deleted my node_modules folder and typed:

{% highlight livescript %}
$ npm install --production
{% endhighlight %} 
    
At this point I could at last run the Ghost app locally without any errors showing up:

{% highlight livescript %}
$ npm start
{% endhighlight %} 

Then I runned:

{% highlight livescript %}
$ git rm -r --cached .
{% endhighlight %} 

Then:

{% highlight livescript %}
$ git add .
{% endhighlight %}

Next

{% highlight livescript %}
$ git status
{% endhighlight %}

And the node_modules folder displayed as deleted from the git directory tree.

{% highlight livescript %}
$ git commit -m 'added my custom theme'
{% endhighlight %}
    
**Note** *To make sure that pushing the local repository to the public site didn't break any of the installations I pushed a folder containing a custom theme.*

{% highlight livescript %}
$ git push
{% endhighlight %}

Then I logged into my live site and was able to select the custom theme from the settings pannel without breaking anything. So the code was uploaded correctly.
