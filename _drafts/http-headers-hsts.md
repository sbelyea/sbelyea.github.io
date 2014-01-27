---
layout: post
title: "HTTP Headers: HSTS"
description: "Adding the HTTP Strict Transport Security Header to Improve Security"
category: articles
tags: [security, web]
<!--image:
  feature: so-simple-sample-image-3.jpg
  credit: Stephen Belyea
  creditlink: http://sbelyea.net-->
comments: true  
---

# What is HTTP Strict Transport Security?

Per the RFC:

> 1. UAs transform insecure URI references to an HSTS Host into secure URI references before dereferencing them.
>
> 2. The UA terminates any secure transport connection attempts upon any and all secure transport errors or warnings.

So, what does this mean in practice?

- If using a browser that respects the header, it (the browser) will switch to use HTTPS instead of HTTP for the domain.

- The browser will also terminate the secure connection upon any errors/problems.[*][1] 

# Why should I use it?
If you're running a web server, you could just as easily force a redirect server-side to force browsers hitting an insecure (HTTP) URL to connect to a secure one (HTTPS).  Configuration differs across web servers.  

Using this header can provide an additional layer of resiliency for browsers that support it.  An errant misconfiguration on your web server won't necessarily result in all traffic being insecure.


[0]:http://www.w3.org/TR/2012/CR-CSP-20121115/#framework
[1]:http://tools.ietf.org/html/rfc6797#section-2.1
[2]:http://ibuildings.nl/blog/2013/03/4-http-security-headers-you-should-always-be-using