---
layout: post
title: "Working with Wraith, Pt. 2"
description: "Spidering"
category: articles
tags: [wraith, front end development, responsive, testing]
<!--image:
  feature: so-simple-sample-image-3.jpg
  credit: Stephen Belyea
  creditlink: http://sbelyea.net-->
comments: true  
---

_This post is an addendum for [Working with Wraith][0]._

If you don't want to spend the time documenting out each URL on your site, [Wraith][1] can perform a spider of your site's links upon execution.  This functionality is courtesy of the [Anemone][2] library that's required for Wraith.

## How to use Wraith's spidering

It's pretty simple: comment out (or remove) the `path:` section of your config.yaml.  For example, if this is your current `path:` section and you want to use spidering, change this:

~~~yaml
paths:
  home: /
  articles: /articles/
~~~

...to this:

~~~yaml
#paths:
#  home: /
#  uk_index: /uk
~~~

## What about exclusions?

In the case of the Jekyll theme I'm using, when spidering my site a `tags.html` page was being created, with additional parameters being tacked on for each of the tags that I've made.  To resolve the issue, you can specify Anemone specific parameters to change the behavior of the spidering.  Open `/lib/wraith_manager.rb` and find the below section of code.

~~~ruby
def spider_base_domain

    spider_list = []
    #set the crawl domain to the base domain in the confing 
    crawl_url = wraith.base_domain
    #ignore urls to file extension such as images etc
    ext = %w(flv swf png jpg gif asx zip rar tar 7z gz jar js css dtd xsd ico raw mp3 mp4 wav wmv ape aac ac3 wma aiff mpg mpeg avi mov ogg mkv mka asx asf mp2 m1v m3u f4v pdf doc xls ppt pps bin exe rss xml)
    Anemone.crawl(crawl_url) do |anemone|
      # Don't spider the tags for a Jekyll blog
      anemone.skip_links_like %r{^/tags.*}
      anemone.on_every_page do |page|
          #puts page.url
          #add the urls to the array
          spider_list << page.url.path
      end
    end
~~~

You can use regular expressions with the aptly named `anemone.skip_links_like` method.  If you want to prevent `/tags*` from appearing in your spider results (like I did), you can use the code below.

`anemone.skip_links_like %r{^/tags.*}`

---
A big thanks to the [BBC][3] [News][4] [developers][5] who created this excellent application and the community who maintains it!  You can find the github page [here][1].

<!-- LINK LIST -->
[0]:http://sbelyea.net/articles/working-with-wraith/
[1]:https://github.com/BBC-News/wraith
[2]:https://github.com/chriskite/anemone
[3]:https://twitter.com/dblooman
[4]:https://twitter.com/jcleveley
[5]:https://twitter.com/sthulb