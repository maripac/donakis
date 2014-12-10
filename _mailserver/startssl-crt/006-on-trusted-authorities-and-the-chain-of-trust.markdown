---
layout: postInSeries
title: "006 On trusted Authorities and the Chain of Trust"
comments: true
date: 2014-11-27 00:21:16
categories: 
index: 006
short-name: "On trusted Authorities"
cabecera: "006 On trusted Authorities and the Chain of Trust"
---

A digital certificate certifies the ownership of a public key by the named subject of the certificate. This allows others (relying parties) to rely upon signatures or on assertions made by the private key that corresponds to the certified public key. In this model of trust relationships, a CA is a trusted third party - trusted both by the subject (owner) of the certificate and by the party relying upon the certificate. 

It will not be very difficult to bear the possibility of some type of collapse concerning this whole structure of trust that might affect the current system of CA (Certificate Authorities) in a short time basis. The chain certificate structure is based on a layered system, whose logic operates mainly by assuming as trustworthy those layers that have been denoted as such by an entity that is located before another in a chained structure. This adds up for a very delicate balance, which is already starting to raise a number of doubts around its own strenght. So, in my personal opinion I would not underestimate the new emerging proposals such as [Let's Encrypt](https://letsencrypt.org/) that hopefully will turn into a reality those better perspectives that will bring to an end the type boundaries that surprisingly enough still make of the internet a place of unequal access to permanent resources. 
