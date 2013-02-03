# JDStraughan.com / jdstraughan.github.com

This is the _site directory contents as output from [jekyll](https://github.com/mojombo/jekyll).

The source for this site can be found at my [jdstraughan.com](https://github.com/JDStraughan/JDStraughan.com) repository.

My site utilizes [jekyll plugins](https://github.com/mojombo/jekyll/wiki/Plugins), and is hosted (for free) on [GitHub Pages](http://pages.github.com/).  This is cool and all, with one drawback: GitHub Pages does not run plugins when it compiles jekyll sites, and does not have a nice override to just use the _sites directory if needed.

There are several solutions to this problem, I found most of them suck.

To solve this problem for myself, I simply have 2 repos:

- [JDStraughan.com](https://github.com/JDStraughan/JDStraughan.com) -> Contains the source for jekyll.
- [jdstraughan.github.com](https://github.com/JDStraughan/jdstraughan.github.com) -> Is just a git repo initialized inside my _site directory in the JDStraughan.com repo.

So now I can make all the changes I want to JDStraughan.com source, commit, etc.  When I am ready to publish (deploy) the changes to by site, I just commit and push the contents of my _site directory to jdstraughan.github.com repo and github publishes my static site.

See Also:
- [GitHub Pages docs](https://help.github.com/categories/20/articles)
- [Setting up a custom domain on GitHub pages](https://help.github.com/articles/setting-up-a-custom-domain-with-pages)
- [Jekyll Liquid Extensions](https://github.com/mojombo/jekyll/wiki/Liquid-Extensions)