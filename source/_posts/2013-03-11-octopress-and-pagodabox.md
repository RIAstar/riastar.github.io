---
layout: post
title: "Moving to Octopress and Pagodabox"
date: 2013-03-11 19:00
comments: true
categories: [blog, CMS, hosting, security]
---

It was bound to happen at some point: **I got hacked**. Don't know when, don't know why. I only know that someone broke
into my server, replaced the index page with a nice picture of the Koran and some Engrish proze praising the love of
God. My islamist visitor was kind enough to leave the rest of my content untouched - for all I know, that is.

##Now how could this happen?

Well, since I can find the time to write on this blog - say - once a year, how often do you think I applied security
updates to my WordPress blog?

Right!

{% img right /images/posts/2013/octopress.png OctoPress logo %}

So, because a Wordpress engine was overkill for my requirements anyway, not to mention slooooooow, I went CMS hunting
and found the perfect solution with [OctoPress][1]. This is a perfectly geeky CMS that generates static html pages.
You write your posts in [MarkDown][2] - oh yeah, pure text, no WYSIWYG involved; then you  execute the `generate`
command on the command line; your site is generated and ready to deploy.

Its main advantages over my previous situation:

 - I love the geekyness of the whole concept
 - It's blazing fast: no database queries, no script processing, no nothing
 - It's way more secure: try injecting this, m\*th\*f\*ck\*r
 - It generates perfect semantic markup
 - So far it's been fairly easy to customize

Main downside at this point:

 - I had a pretty hard time getting it to run on my Windows box: it's written in Ruby which doesn't like Windows very
 much. And I had no luck at all installing it under [Cygwin][3], so for the time being I'm stuck in a DOS shell to
 issue my commands.

##New server

By chance I came across [PagodaBox][4] just about the same day. It is to Php what [Heroku][5] is to Ruby: a scalable
hosting service in the cloud. Fortunately they had a free offering that - though limited - allowed me to play
around. And I really love it, mostly for its Git deployment interface.

The result is that I ended up with a workflow to write a new post and deploy it to my [PagodaBox][4] instance, that
goes like this:

``` sh
$ vim 2013-03-11-my-new-post.md
$ rake generate
$ git add .
$ git commit -m 'created new blog post'
$ git push
```

Just lovely!

[1]: http://octopress.org/ OctoPress
[2]: http://daringfireball.net/projects/markdown/ MarkDown
[3]: http://www.cygwin.com/ Cygwin
[4]: https://pagodabox.com/ PagodaBox
[5]: http://www.heroku.com/ Heroku
