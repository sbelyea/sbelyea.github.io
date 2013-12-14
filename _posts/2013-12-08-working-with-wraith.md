---
layout: post
title: "Working with Wraith"
description: "Going beyond the Readme.md"
category: articles
tags: [wraith, front end development, responsive, testing]
<!--modified: 2013-12-08-->
<!--image:
  feature: so-simple-sample-image-3.jpg
  credit: Stephen Belyea
  creditlink: http://sbelyea.net-->
comments: true  
---

Building a responsive website requires testing at multiple resolutions to make sure your site doesn't break.  The fine developers at BBC News have created [Wraith][1], a Ruby application that allows you to quickly snap images of your website at any resolution(s) you may desire.  Unfortunately, the read-me on the repository doesn't provide _all_ the information that you may need to get Wraith running properly for your site.  

That's where this post comes in.  I'll cover some of the additional functions that aren't clearly documented on the [repository page][1].  Specifically:
	
- Working with page file extensions
- Changing the user agent
- Setting cookies

_Note: this post assumes that you've already configured Wraith on your local machine following the directions provided on the [repository page][1], and that you're somewhat unfamiliar with Ruby (as I am).  It is meant to be a supplement to the read-me, not a replacement.  I make the assumption that you are using phantom.js as your headless browser; slimer.js directions may be different._

## Working with page file extensions

If you already know what pages you want to capture with Wraith, you can modify `config.yaml` to add them in the `paths:` section.

~~~yaml
paths:
  home: /
  uk_index: /uk
~~~

Suppose that your website requires you to put the specific page _and_ the file extension?  Simply enclose the paths in double quotes ("") to ensure that Wraith can properly parse the file paths.

~~~yaml
paths:
  home: "/default.html"
  uk_index: "/uk/default.html"
~~~

## Changing the user agent

The user agent for phantom.js can be changed by modifying `snap.js`.

The default user agent is set to:

~~~javascript
page.settings.userAgent = 'Mozilla/5.0 (Macintosh; Intel Mac OS X 10_8_2) AppleWebKit/537.17 (KHTML, like Gecko) Chrome/28.0.1500.95 Safari/537.17';
~~~

This string specifies your browser as an OS X desktop browser.  Modify the `page.settings.userAgent` string to the user agent of your choosing.

For example, to spoof as an iPhone:

~~~javascript
page.settings.userAgent = 'Mozilla/5.0 (iPhone; CPU iPhone OS 7_0 like Mac OS X) AppleWebKit/537.51.1 (KHTML, like Gecko) Version/7.0 Mobile/11A465 Safari/9537.53';
~~~

## Setting cookies

Inside the same `snap.js` file you can specify data to be stored as cookies for phantom.js.  Adding cookies requires the execution of the phantom.js `phantom.addCookie()` method.  You can specify name, value, domain and expiration values for the cookie.

~~~javascript
phantom.addCookie({
     'name': 'myCookie',
     'value': '234A5D265FB9598',
     'domain': 'www.mywebsite.com'
     'expires':  (new Date()).getTime() + (1000 * 60 * 60) 
 });
~~~

This is _especially_ helpful if your site sets cookies to identify authenticated users.

---
A big thanks to the [BBC][2] [News][3] [developers][4] who created this excellent application and the community who maintains it!  You can find the github page [here][1].

<!-- LINK LIST -->
[1]:https://github.com/BBC-News/wraith
[2]:https://twitter.com/dblooman
[3]:https://twitter.com/jcleveley
[4]:https://twitter.com/sthulb