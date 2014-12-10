---
layout: postInSeries
title: "000 Intro and resources"
comments: true
date: 2014-11-27 19:21:16
categories: 
index: 000
short-name: "Intro and resources"
cabecera: "000 Introduction"
---

I am writting this post in order to document the initial setup that I performed in the production environment where this site is hosted. The purpose that I had in mind when I decided to setup the whole environment from scratch was just learning, as well as seing by first hand what the other side looks like. I have no knowledge at all on system administration, or network security. I did some research in order to find good documentation. In my opinion, as well as those that I have read in different forums the [ISPmail tutorials series](https://workaround.org/ispmail) is one of the most reliable and complete collection of documents on mailserver setup, that you can find on the internet &mdash;and it also includes some tips and recommendation on mailserver administration and maintenance&mdash;. 

It is not difficult to find other tutorials on the internet that adapt the ISPmail guide to other type of environments, as this [tutorial](https://www.digitalocean.com/community/tutorials/how-to-configure-a-mail-server-using-postfix-dovecot-mysql-and-spamassasin) for DigitalOcean droplets running Ubuntu. 

[Linode](https://www.linode.com/) has a great collection of guides and tutorials, very well designed and complete. This [guide](https://www.linode.com/docs/email/email-with-postfix-dovecot-and-mysql) was very helpfull, but I used it as a supporting guide for the ISPmail one, because it amplifies some concepts and explains a bit more in deep some information in a way that for someone with a lower level of experience like me, results easier to understand. At some point, it's important to notice that these two guides differ in the steps taken to approach the configuration of the mailserver. In my case I must say that the approach that worked out first was the one recommended in the ISPmail guide, so I did not experiment further with the configuration explained in the Linode guide. 

Anyway, no matter how well you prepare and do your research before you start the type of setup a mailserver requires, there are many chances that you will have to take your time untill all the pieces work togheter.

<blockquote><q>
Pataphysics will be, above all, the science of the particular, despite the common opinion that the only science is that of the general. Pataphysics will examine the laws governing exceptions, and will explain the universe supplementary to this one.
</q></blockquote>

<!-- And that has not really much to do with following one or another guide but mostly with those aspects of your environment, as well as the circumstancial coincidence of aspects that can be no problem at all when they exist separately, but when found togheter suddenly turn into a huge hurdle. -->

In the following sections of this document I will just go through the steps as they were performed in my system, mostly as a precautionary measure that will serve as a record, in case of future problems. In addition, I will provide some step by step instructions of some aspects that I found a bit vaguely described.