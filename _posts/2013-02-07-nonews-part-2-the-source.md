---
layout: post
published: true
title: NoNews Part II - The Source
tags: ["chrome extension", "javascript"]
excerpt: "NoNews is a Google Chrome extension that was built to assist me (and you) avoid passively reading the news.  I wrote a blog post about why I wrote it, and explored the inspiration behind the endeavor.  In this post I'd like to walk-through the source code, explain how the extension is constructed, and talk about things that can be done to improve the extension."
header-img: "img/header/badnews2.jpg"
image-credit: Benjamin Vautier [Public domain], <a href="http://commons.wikimedia.org/wiki/File%3ABenjamin_Vautier_Die_Neuigkeiten.jpg">via Wikimedia Commons</a>
---

[NoNews is a Google Chrome extension](https://chrome.google.com/webstore/detail/no-news-is-good-news/fnikidjfogfllkinoahanihoddalbhil) that I built to assist me (and you) in avoiding passively reading the news while casually surfing the web.  I wrote a [blog post about why I made it](http://jdstraughan.com/2013/01/29/nonews-is-good-news/), and explored the [inspiration](http://www.aaronsw.com/weblog/hatethenews) behind the endeavor.  In this post I'd like to walk-through the [source code](https://github.com/JDStraughan/nonews), explain how the extension is constructed, and talk about things that can be done to improve the product.

This was my first attempt at creating a browser plugin, and I relied heavily on the [developer documentation](http://developer.chrome.com/extensions/) provided by Google.  At first glance it seemed lacking, and while the organization and example code was not always fulfilling, the docs do provide all the information needed to begin the journey into extension creation. Being my first extension, I am not sure I followed all the best practices or chose the best route for accomplishing my task, however, with a small amount of code I was able to create a fully functioning plugin with a few features with very little time invested. Experienced extension developers: I look forward to your constructive criticism in the comments!

## The Code

The biggest challenge I faced was understanding the difference between [manifest versions 1 and 2](http://developer.chrome.com/extensions/manifestVersion.html).  More importantly, I would find example code written for manifest v1, and attempt to make it work in v2.  This was the source of much frustration.  NoNews is written using manifest v2, so hopefully you don't have the same issues I did.

Note: if there are future updates and you need to match source code, this walkthrough is using [tagged version 0.5 of NoNews](https://github.com/JDStraughan/nonews/tags).

Speaking of manifest files, lets take a look at <code>manifest.json</code>:

{% highlight javascript %}
{
  "name": "No News (is Good News!)",
  "manifest_version": 2,
  "version": "0.5",
  "homepage_url" : "http://www.nonews.info",
  "description": "Warns you of news pages that may ruin your day ;)",
  "icons": { "16": "img/icon16.png",
             "48": "img/icon48.png",
            "128": "img/icon128.png" },
  "browser_action": {
    "default_icon": {
      "19": "img/icon19.png",
      "38": "img/icon38.png"
    },
    "default_title": "No News (is Good News!)",
    "default_popup": "html/popup.html"
  },
  "permissions": [
      "webRequest", "webRequestBlocking", "http://*/", "https://*/"
  ],
  "background": {
    "scripts": ["js/nonews.js"]
  }
}
{% endhighlight %}

JSON should be familiar to all of us, and it is pretty easy to see what is going on for most of this file.  The manifest defines and configures things like the name, url, asset locations, permission settings, manifest_version, etc. We will need to refer back to this file though out the build to explain each section, but this is what the final product looks like. The developer docs have a great [summary of manifest options](http://developer.chrome.com/extensions/manifest.html) that explains the different settings available.

It is also worth mentioning that there is not a strict directory structure for extensions, with many simply having all the files in the root directory.  For this extension, I am using a file structure that looks like this:

    .
    ├── README.md
    ├── assets
    │   └── blacklist.json
    ├── changelog.txt
    ├── css
    │   └── popup.css
    ├── html
    │   └── popup.html
    ├── img
    │   ├── icon128.png
    │   ├── icon48.png
    │   └── icon16.png
    ├── js
    │   ├── nonews.js
    │   └── popup.js
    └── manifest.json

Most of this is self explanatory, but let's do a quick overview of the files.  The <code>README.md</code> file is there for GitHub, since this is an open-source project. Next up is the <code>assets</code> directory, which contains one file: <code>blacklist.json</code>.  This file is responsible for seeding the extension with a list of URLs that will be blocked by the browser.  The <code>css/popup.css</code>, <code>html/popup.html</code> and <code>js/popup.js</code> files generate the the popup display that occurs when a user places their mouse over the icon next to the address bar.  The icons listed in the <code>/img</code> directory fulfill the sizes required as shown [here](http://developer.chrome.com/extensions/manifest.html#icons).  The <code>js/nonews.js</code> file holds all the logic for our extension, and we are already familiar with the <code>manifest.json</code> file.

Now that we have the directory structure and manifest file out of the way, let's dive into the most important file in the extension: the event page.  The docs tell us that through v1, extensions had the option to run a background script that continually monitored and reacted on browser events.  In v2, we have the ability to set persistence on event pages - a persistent event page is a background page, and setting persistent to false let's us have an event page that is only loaded as needed.  For more information, read the [documentation on event pages](http://developer.chrome.com/extensions/event_pages.html).

Looking back at the <code>manifest.json</code> file, we see that we have a non-persistant background page located at <code>js/nonews.js</code>.

{% highlight javascript %}
  ...
  "background": {
    "scripts": ["js/nonews.js"]
  }
  ...
{% endhighlight %}

Since the default in v2 is <code>"persistence": false</code>, it is not being explicitly set here.  The docs tell us what this means in terms of when our extension will be loaded:

> - The extension is first installed or is updated to a new version (in order to register for events).
> - The event page was listening for an event, and the event is dispatched.
> - A content script or other extension sends a message.
> - Another view in the extension (for example, a popup) calls chrome.runtime.getBackgroundPage().

So this script is a perfect place to setup our extension, and add any event listeners we may need.  Let's take a loot at this event page.

#####js/nonews.js

{% highlight javascript linenos %}
if (typeof localStorage.nonews_blacklist_urls === "undefined") {
  // Get the file for the blacklist to load from json
  var fileURL = chrome.extension.getURL("../assets/blacklist.json");
  var xmlreq = new XMLHttpRequest();
  xmlreq.open("GET", fileURL, false);
  xmlreq.send();

  // The file should be in xmlreq.responseText
  localStorage.nonews_blacklist_urls = xmlreq.responseText;
}

// Add the event listener with filters
chrome.webRequest.onBeforeRequest.addListener(
  function(tab) {
    // Check for manual bypass via query string
    if (tab.url.indexOf("nonews_bypass=true") > 0) {
      return;
    }
    // Check for snooze option
    now = new Date().getTime();
    if (localStorage.nonews_snooze_end > now) {
      return;
    }
    // Redirect to blocked site page, passing url for bypass option
    return {
      redirectUrl: 'http://www.nonews.info/blocked-site.html?site=' + tab.url
    }
  },
  // filters
  {
    urls: JSON.parse(localStorage.nonews_blacklist_urls),
    types: ["main_frame"]
  },
  // extratabSpec
  ["blocking"]
);
{% endhighlight %}

That, ladies and gentlemen, is the bulk of the code to make this extension work.  A handful of lines of native javascript using the Chrome API is all that is required to make this thing go.  Breaking it down into sections makes it easier to consume:

{% highlight javascript linenos %}
if (typeof localStorage.nonews_blacklist_urls === "undefined") {
  // Get the file for the blacklist to load from json
  var fileURL = chrome.extension.getURL("../assets/blacklist.json");
  var xmlreq = new XMLHttpRequest();
  xmlreq.open("GET", fileURL, false);
  xmlreq.send();

  // The file should be in xmlreq.responseText
  localStorage.nonews_blacklist_urls = xmlreq.responseText;
}
{% endhighlight %}

Starting with line 1, I am checking to see if we have already stored the blacklist into [localStorage](http://en.wikipedia.org/wiki/Web_storage#Local_and_session_storage).  This is not incredibly important for this version of the extension, as we could just load the blacklist into a variable that will be used in the event listener, however, adding to localStorage allows us to easily manipulate this list in the future, possibly through and [options page]() interface.

If the black list is not already loaded, lines 3-6 use a <code>XMLHttpRequest</code> to load the <code>assets/blacklist.json</code> file.  Before we move on, let's take a quick look at the blacklist file:

{% highlight javascript %}
{
	"urls" : [
		"*://*.10news.com/*",
		"*://*.10tv.com/*",

        ...

		"*://*.zoiksonline.com/*",
		"*://*.zwire.com/*"
	]
}
{% endhighlight %}

The actual file is over 6300 lines long, containing most of the websites from [this list of Google News sources](http://www.labnol.org/tech/google-news-sources/19321/). I am sure you have observed that these are not URLs, rather they are [match patterns](http://developer.chrome.com/extensions/match_patterns.html) that define our blacklist of sites.

In line 9 of the <code>nonews.js</code> file we see that the blacklist is loaded as a string into <code>localStorage</code>, and is now available for our extension to use.  Moving on we see the 2nd half of the event page:

{% highlight javascript %}

// Add the event listener with filters
chrome.webRequest.onBeforeRequest.addListener(
  function(tab) {
    // Check for manual bypass via query string
    if (tab.url.indexOf("nonews_bypass=true") > 0) {
      return;
    }
    // Check for snooze option
    now = new Date().getTime();
    if (localStorage.nonews_snooze_end > now) {
      return;
    }
    // Redirect to blocked site page, passing url for bypass option
    return {
      redirectUrl: 'http://www.nonews.info/blocked-site.html?site=' + tab.url
    }
  },
  // filters
  {
    urls: JSON.parse(localStorage.nonews_blacklist_urls),
    types: ["main_frame"]
  },
  // extratabSpec
  ["blocking"]
);
{% endhighlight %}

Here we are using the [chrome.webRequest](https://developer.chrome.com/extensions/webRequest.html) module to create our filters. According to the docs:

> You must declare the "webRequest" permission in the extension manifest to use the web request API, along with host permissions for any hosts whose network requests you want to access.

To accomplish this, we have this section in the <code>manifest.json</code> file:

{% highlight javascript %}
  ...
  "permissions": [
      "webRequest", "webRequestBlocking", "http://*/", "https://*/"
  ],
  ...
}
{% endhighlight %}

This gives permission to <code>chrome.webRequest</code>, <code>chrome.webRequestBlocking</code>, and all URLs using <code>http|https</code>.  We'll be using [webRequestBlocking](https://developer.chrome.com/extensions/webRequest.html#type-BlockingResponse) to stop the request from executing, and instead we'll redirect the user elsewhere.  Before we cover that, we back up and look at the event listener.

In the webRequest module, we are adding an event listener to the <code>onBeforeRequest</code> which the docs tell us:
> Fires when a request is about to occur. This event is sent before any TCP connection is made and can be used to cancel or redirect requests.

That is exactly what we need to stop the browser from displaying a page that originates at a site in our blacklist. The <code>addListener</code> method is available via the [chrome.events](https://developer.chrome.com/extensions/events.html) module, and the docs tell us:

> The argument to addListener() is always a function that you define to handle the event, but the parameters to the function depend on which event you're handling.

In the case of <code>chrome.webRequest.onBeforeRequest</code>, the addListener method takes these arguments: <code>callback</code>, <code>filter</code>, and <code>opt_extraInfoSpec</code>. The callback is the anonymous function we are using as the first argument, we are passing an object listing our filtered URLs in the second argument, and an array containing one value, "blocking", in the third.

Working backwards, let's look at this "blocking" array first. The docs tell us:

> If the optional opt_extraInfoSpec array contains the string 'blocking' (only allowed for specific events), the callback function is handled synchronously. That means that the request is blocked until the callback function returns.

Perfect! So if there is a match in the filters, the request will be blocked until the callback is completed.  Let's look at the filters next.

{% highlight javascript %}
  ...
  // filters
  {
    urls: JSON.parse(localStorage.nonews_blacklist_urls),
    types: ["main_frame"]
  },
  ...
{% endhighlight %}

The [request filter](https://developer.chrome.com/extensions/webRequest.html#type-RequestFilter) requires a <code>urls</code> property containing a list of URLs or URL patterns, and has 3 optional properties.  We'll use the optional <code>types</code> property to define <code>main_frame</code> as the request type we are limiting our filter to.  Notice to assemble the list of urls, we are just parsing the locally stored string into a JSON format.

Finally, we are at the heart of the extension, the callback that will be fired when a request is blocked.

{% highlight javascript %}
  ...
  function(tab) {
    // Check for manual bypass via query string
    if (tab.url.indexOf("nonews_bypass=true") > 0) {
      return;
    }
    // Check for snooze option
    now = new Date().getTime();
    if (localStorage.nonews_snooze_end > now) {
      return;
    }
    // Redirect to blocked site page, passing url for bypass option
    return {
      redirectUrl: 'http://www.nonews.info/blocked-site.html?site=' + tab.url
    }
  },
  // filters
  ...
{% endhighlight %}

We are passing the <code>tab</code> object to the function so we can made some decisions based on the contents of that object.  Although we have not looked at the features for bypassing and snoozing the extension, the code is not incredibly difficult.

First, we are checking to see if the query string contains <code>nonews_bypass=true</code>, and if so, we are simply returning and allowing the browser to go on its merry way.

Next, we are setting <code>now</code> to reflect the current unix timestamp, and asserting that if there is a <code>localStorage</code> variable named <code>nonews_snooze_end</code>, and it is larger than <code>now</code>, then we get out of the way and let the user go.

If none of these conditions are met, we simply redirect the user to a web page that serves as the blocked page, and we pass a query string setting the original request URL.

The public facing site for NoNews is available in the [website branch of the repository](https://github.com/JDStraughan/nonews/tree/website). It is a simple, static HTML site that can be easily hosted on places like GitHub Pages or S3, so you will not need any special web server on you local machine to view.  Just open pages in your browser and you'll be good to go.

Currently, the blocked page looks like this:

![Nonews Blocked Site Screen](/img/post/nonews-blocked-site-screen-800.jpg)

>Screenshot of blocked URL with red bypass button and snooze bar popup open.

In this screen shot, you can see the red button on the blocked site page that touts the ability to allow you to continue on your way.  This is accomplished by some javascript in the website branch that creates a link using the original request URL with <code>nonews_bypass=true</code>added as part of the query string.  This creates a link that will successfully meet the requirement in the callback function to temporarily disable blocking for the request.

In the upper righthand portion of the image, we can see the NoNews snooze bar.  This area is created when someone clicks on the NoNews icon that is added to the right of the address bar.  Going back to the manifest we see:

{% highlight javascript %}
  ...
  "browser_action": {
    "default_icon": {
      "19": "img/icon19.png",
      "38": "img/icon38.png"
    },
    "default_title": "No News (is Good News!)",
    "default_popup": "html/popup.html"
  },
  ...
{% endhighlight %}

The [browser action](http://developer.chrome.com/extensions/browserAction.html) module is described as:

> Use browser actions to put icons in the main Google Chrome toolbar, to the right of the address bar. In addition to its icon, a browser action can also have a tooltip, a badge, and a popup.

The icons are defined by their names as a convention for Chrome to know their size.  The <code>default_title</code> will be the tool tip text shown on hover, and the <code>default_popup</code> contains the location of the page to be loaded when the icon is clicked.

<h5>html/popup.html</h5>

{% highlight html %}
<link rel="stylesheet" type="text/css" href="../css/popup.css">
<div id="popup">
	<h3>No News (is Good News)</h3>
	<p>
		A friendly reminder that reading the news is not always a good thing.
	</p>
	<p>
		Inspiration: <a href="http://www.aaronsw.com/weblog/hatethenews" target="_blank">This essay</a> by <a href="http://en.wikipedia.org/wiki/Aaron_Swartz" target="_blank">Aaron Swartz</a>.
	</p>
	<p>
		No News is open source! Visit our <a href="https://github.com/jdstraughan/nonews" target="_blank">GitHub Page</a>.
	</p>
	<p id="snooze_container"><p>
</div>
<script type="text/javascript" src="../js/popup.js"></script>
{% endhighlight %}

This is a pretty straightforward HTML file, with the only notable areas being the <code>snooze_container</code>, and the javascript file that is loaded at the end of the file.

#####js/popup.js
{% highlight javascript %}
var now = new Date().getTime();
var snooze_end = localStorage.nonews_snooze_end;
setSnoozeContents(now, snooze_end);
var el = document.getElementById("nonews_snooze");

if (el) {
  el.addEventListener("click", function() { snooze(now, snooze_end) });
}

// Set the snooze period to stop filter from blocking sites
function snooze(now, snooze_end) {

  // Do nothing if stored is still valid
  if (snooze_end > now) {
    return;
  }

  // Set new snooze time (in unix timestamp (in milliseconds))
  // Set for 15 minutes in the future (90000ms = 15mins)
  localStorage.nonews_snooze_end = now + 900000;

  // Update the class to reflect snooziness
  setSnoozeContents(now, localStorage.nonews_snooze_end);

}

// Human readable amount of time left in snooze period
function snoozeTimeRemaining(now, snooze_end) {
  secs_remain = Math.ceil((snooze_end - now) / 1000);
  if (secs_remain < 60) {
    return secs_remain + ' seconds';
  } else {
    mins_remain = Math.ceil(secs_remain / 60);
    unit_of_measure = (mins_remain == 1) ? ' minute' : ' minutes';
    return mins_remain + unit_of_measure;
  }
}

// Updates the snooze container with proper html to operate snooze bar
function setSnoozeContents(now, snooze_end) {
  if (snooze_end > now) {
    contents = '<span class="snoozing">NoNews is snoozing for  the next ' + snoozeTimeRemaining(now, snooze_end) + '.</span>';
  } else {
    contents = '<button id="nonews_snooze" href="#">Snooze NoNews Filtering for 15 Minutes</button>';
  }
  document.getElementById("snooze_container").innerHTML = contents;
}
{% endhighlight %}

This file is not nearly as important for the core functionality of the extension, so I'll go over it in less detail.  This just handles the snooze functionality, more specifically: setting the snooze end time, and showing a snooze bar or a time remaining in a snooze cycle.

First, we set timestamps for <code>now</code>, and <code>snooze_end</code>.  Next, we create the contents of the snooze bar area with the <code>setSnoozeContents()</code> function.  This function fills the paragraph element with either a button that will be our snooze bar, or a reminder of how long is left in the current snooze cycle.

We then add an event listener to the snooze bar, if it exists, that calls the <code>snooze()</code> function on a click event.  First snooze checks to make sure it is not already snoozing, then sets the <code>localStorage.nonews_snooze_end</code> variable to 15 minutes in the future, in milliseconds.  Once this has been set, the <code>setSnoozeContents()</code> method is fired again, as the snooze bar needs to be replaced with the time remaining.

The <code>snoozeTimeRemaining()</code> function should be self-explanatory; it simply returns a human readable representation of time remaining until the snooze cycle is complete.

## What's next?

This project is still in its infancy.  I hope.  I am using it now, and it is serving me well.  I would like to see some additional features, like an options page that allows the blacklist to be manipulated, or safe times to be set where the extension is automatically snoozed during certain times.  I've thought about adding importing and exporting of blacklists, custom whitelists, and custom redirect pages.  The list goes on.

I don't have to be the creator of all these new features.  The [NoNews extension is open-source](https://github.com/JDStraughan/nonews), and all pull requests will be considered for inclusion.

##Conclusion

With a little bit of javascript, some time to delve into the documentation, and some experimentation, creating a browser extension can be a fun and rewarding process.  This extension is a very simplistic one, yet it served as a good introduction to how these things are constructed.

Paying attention to the permissions in the manifest file and exploring different aspects of the API was the key for me when poking around with the idea of making this extension.  I tried a few different ways to accomplish this task, and had fun breaking things and getting them all right again.  I hope this guide helps you in your browser extension adventures, and please leave any constructive criticism, improvements, or questions in the comments section below.
