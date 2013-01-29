---
layout: post
published: true
title: Jekyll is just what the doctor ordered
tags: ["jekyll", "liquid"]
excerpt: "New year, new blog, new blog engine.  For the last few years, my blog has successfully sat stagnant with 2 posts in it.  Despite my total lack of attention to this site, it still managed to garner a few dozen readers a day, mainly from stackoverflow and some Russian blog. For several months I have been eyeing jekyll, the blog engine from Tom Preston-Werner of GitHub fame."
---

### Strange Case of mixed personalities

Like many web developers I find blogs, or more specifically blog engines, to be a sore subject.  Many of us have worked with a wide variety of CMS (content management system) or blog engines, things like WordPress, Drupal, Joomla, or countless others, over the course of our careers.  We have had heated debates over which is a framework, a library, a CMS, or a blogging platform.  We have explored, hacked, created plugins, and at some point wrangled these monstrous codebases into something that halfway resembles something nice.  And it is all about the lie: _anyone can update the site now_.  Which is not completely a lie, it is more of a half truth - anyone can completely destroy your hard work with just a few animated unicorn gifs and 16 different colors for text.  The worst part about using a canned blogging system is you have to use it.

The other side to this story is most of us have written many, maybe dozens, of custom CMS solutions for clients over the years.  We've developed sites and apps with [scaffolding](http://en.wikipedia.org/wiki/Scaffold_%28programming%29), simple [CRUD](http://en.wikipedia.org/wiki/CRUD) management, full blown CMSes, blog solutions, and sometimes *the works*.  I've personally written them in several languages and frameworks, more than I'd care to list.  My last blog was a custom CMS written in PHP using Kohana, and while I loved it, like most side projects I just did not always have the time to keep it up to date.  For the past 18 months or so, I have been writing Ruby almost exclusively - and on my short list has been replacing the blog I never use with a new one I'll never use, only this time in ruby.  I know, not the best of goals.

So I am stuck at a crossroads: do I continue to reinvent the wheel knowing I'll never have the time to do it right, or do I give in and use a big, bloated, crappy piece of software to run a blog no one will ever read?

### The devil is in the details

For those of you that don't know, [Tom Preston-Werner](http://tom.preston-werner.com/) (of GitHub fame) created a blog engine that [GitHub Pages](http://pages.github.com/) uses called [jekyll](https://github.com/mojombo/jekyll). According to the readme file:

> Jekyll is a simple, blog aware, static site generator.

At first glance you may think this is some hipsterware for blogging old school.  On the contrary, jekyll overcomes many problems by being simple, and static.

A dynamic page is one that is backed by server-side code that is executed and renders a page for each request.  This has many advantages that stem from having page templates populated with data from databases that are managed by even more dynamic pages.  Server side languages like php, ruby, python, etc. are well suited for creating rich web applications, and many blogs fit into that category.  There are disadvantages also: servers, maintenance, upgrades, security - these things can cost time and money.  There is also scalability: if a page or blog becomes too popular, the cost of more databases and web servers becomes a burden, and caching solutions are required.  

So if you are following along, to run a self-hosted blog you will need servers, databases, and eventually caching.  Caching can be as simple as publishing all your posts as static html files and serving them on a CDN or something like Amazon's S3, or as complex as caching servers that do all of this stuff for you, for a price.  

### Doctoring jekyll saves my hide

Jekyll solves this entire problem by skipping the dynamic step and jumping straight to the static page part.  

The part with static html pages, just like 1996.  

Only better.  Way better.

Unlink anything 1996, jekyll is _blog aware_.  It is scriptable through [Liquid](http://liquidmarkup.org/) and extendable via [plugin system](https://github.com/mojombo/jekyll/wiki/Plugins). Best of all, plugins are just ruby scripts, so making your own is quite simple.  To get a jekyll blog up and running, all you need to do is install jekyll, create some posts, a layout, and voila: you have a blog.  It can get much more complex, but from 10,000 feet, jekyll is infinitely easier than any of the big blogging systems for a personal style blog.

What jekyll gives you for free is an incredibly fast, infinitely scalable, fun and easy to maintain blog that can be hosted for [free on GitHub](http://pages.github.com/), on [AWS S3](http://aws.typepad.com/aws/2011/02/host-your-static-website-on-amazon-s3.html), or anywhere html is served.

It took me a few hours to wrap my head around Liquid.  It took a day of poking around the code and [generating fake posts](https://gist.github.com/4666195) to get everything looking right. In the end I think jekyll solves my split personality issue: I have a blog engine that I have complete control over that does not get in my way, and it enables me to write like a developer.

I am happy to say this site is [powered by jekyll](https://github.com/mojombo/jekyll/wiki/Sites).  Thanks [to all the contributors](https://github.com/mojombo/jekyll/graphs/contributors) for such a great product.