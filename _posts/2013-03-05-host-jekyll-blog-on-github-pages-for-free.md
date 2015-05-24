---
layout: post
published: false
title: Host your blog on GitHub Pages (for free!)
tags: ["jekyll", "blogging", "github"]
description: "Learn how to host your blog or site using the Jekyll blogging engine and GitHub pages for (free) hosting."
---

This blog, like many others, is created using [jekyll](http://jekyllrb.com/), and hosted on [GitHub Pages, for free](http://pages.github.com/).

I'm not special, there is nothing amazing about what is happening here. Here I'll attempt to walk you through all the steps required to make your very own static blog with free hosting.

###What is jekyll?

[Tom Preston-Werner](http://tom.preston-werner.com/) (the GitHub guy) made the static site generating, blog aware, pinnacle of blog badassery called [jekyll](https://github.com/mojombo/jekyll).  This little blog engine that could is great for sites like the one you are currently visiting.  According to the README file:

> Jekyll is a simple, blog aware, static site generator. It takes a template directory (representing the raw form of a website), runs it through Textile or Markdown and Liquid converters, and spits out a complete, static website suitable for serving with Apache or your favorite web server. This is also the engine behind GitHub Pages, which you can use to host your project’s page or blog right here from GitHub.

Because jekyll outputs static files (HTML, css, js, etc), you can host it anywhere, and it is ready for CDN delivery from places like Amazon.  [Installing jekyll](https://github.com/mojombo/jekyll/wiki/install) is easy, there are [great docs in the jekyll wiki](https://github.com/mojombo/jekyll/wiki), and even examples on [converting your legacy blog to jekyll](https://github.com/mojombo/jekyll/wiki/blog-migrations).

###GitHub has Pages?

One of the lesser known features in the GitHub universe is [pages.github.com](http://pages.github.com/).  The official page reads:

>Github Pages are hosted free and easily published through our site, the GitHub for Mac app, or from the command line. Manage your site’s content from GitHub using the tools and workflow that you’re familiar with, so you won’t skip a beat.

It goes on to say:

> There are a few ways to create GitHub Pages, including manually pushing html or a Jekyll site. You can easily redirect your page to a custom url. 

So there we have it: not only can we publish a website on GitHub, we can also use jekyll and a custom URL.

###Publishing jekyll to GitHub

In the GitHub help page [Using Jekyll with Pages](https://help.github.com/articles/using-jekyll-with-pages) it says:

> Every GitHub Page is run through Jekyll when you push content to your repo. Because a normal HTML site is also a valid Jekyll site, you don't have to do anything special to keep your standard HTML files unchanged.

Getting your site on Pages is covered in a different document: [User, Organization and Project Pages](https://help.github.com/articles/user-organization-and-project-pages).  It tells us that for user and organization pages: 

    * This repo must use the username/username.github.com naming scheme.
    * Content from the master branch will be used to build and publish the Pages.
    
This page is very important.  I'd read it twice.

###Using a custom URL

Although the _username.github.com_ naming convention works, and it is nice and all, having a custom domain name is probably preferred by most bloggers.

You could just point a CNAME for _www_ to your _username.github.com_, but then you do not have a canonical URL. If you want to have your naked URL, your www URL, and your _username.github.com_ URL all go to the same place, GitHub makes it easy for you.

The GitHub help pages has a section for [Setting up a custom domain with Pages](https://help.github.com/articles/setting-up-a-custom-domain-with-pages) that will walk you through getting the DNS all squared away.

###Jekyll Plugins

Plugins make jekyll cooler.  The [official jekyll plugins page](https://github.com/mojombo/jekyll/wiki/Plugins) starts off by saying:

>The Jekyll plugin system hooks allow you to create custom generated content specific to your site. You can run custom code for your site without having to modify the Jekyll source itself.

Best of all, plugins are written in Ruby.  The page also lists many plugins available for use, or you can always check out [jekyll-plugins.com](http://jekyll-plugins.com), one of my side projects.

####Publishing Jekyll site with Plugins to GitHub Pages

GitHub likes jekyll, jekyll like plugins, GitHub does not do jekyll plugins.  The GitHub Pages jekyll installation does not execute plugins, so if you are using them, you'll need to output your site first, then upload the static portion.  There are several workarounds out there.

###Conclusion

Compared to dynamically generated blogs that end up having cacheing solution implemented at some point, jekyll rocks.
