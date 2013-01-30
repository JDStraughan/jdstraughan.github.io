---
layout: post
published: true
title: NoNews (is Good News!)
tags: ["chrome extension", "javascript"]
excerpt: No news, as the title and aphorism declare, is often good news.  Like many internet users, reading the news online has become a passive activity for me. I find myself checking news sites when I have 30 seconds to kill, for no reason in particular.  When the actual news airs on television, on the few occasions when I happen to catch it, I find myself secretly enjoying that I am so "plugged in" the news is ancient history by the time the TV gets ahold of it. An essay by Aaron Swartz made me rethink my constant news exposure, and drove me so far I created a browser extension to aid me in changing my ways.
---

No news, as the title and aphorism declare, is often good news.  Like many internet users, reading the news online has become a passive activity for me. I find myself checking news sites when I have 30 seconds to kill, for no reason in particular.  When the actual news airs on television, on the few occasions when I happen to catch it, I find myself secretly enjoying that I am so "plugged in" the news is ancient history by the time the TV gets ahold of it.

I read it all: [HuffPo](http://www.huffingtonpost.com/) to [Drudge](http://drudgereport.com), [Al Jazeera](http://www.aljazeera.com) to [Haaretz](http://www.haaretz.com), [Google News](https://news.google.com) to [/r/worldnews](http://www.reddit.com/r/worldnews/), and everything in between.  I don't favor right wing or left, American or European, I read it all. It's been so bad that I've even read [British tabloids](http://www.thesun.co.uk/).

It is what I do.

Rather, it is what I did.

In one of my recent news binges - sometimes they last hours - I had read the tragic story of [Aaron Swartz](http://en.wikipedia.org/wiki/Aaron_Swartz), a story that ends with a young man taking his own life.  I had never met this man, nor did I know much about him, but the articles touted him as a co-founder of one of my favorite hangouts on the internet: [reddit](http://www.reddit.com).  I don't want to delve too far into his story here, but I would encourage you to research it if you are not familiar with it already.  I do, however, want to talk about one essay I stumbled across while feeding my news binge with every thing I could learn about this troubled young man.

The essay I read was: [I Hate the News](http://www.aaronsw.com/weblog/hatethenews), a piece Aaron had written in October of 2006.

It hit me in the gut.

While the entire essay is worth reading, multiple times, things really started to hit home when he began to persuade that the news is actually harmful, if by no other means than wasting my time.

> With the time people waste reading a newspaper every day, they could have read an entire book about most subjects covered and thereby learned about it with far more detail and far more impact than the daily doses they get dribbled out by the paper. But people, of course, wouldn’t read a book about most subjects covered in the paper, because most of them are simply irrelevant.

Another way to look at this would be: if I replaced the time I waste everyday reading about inconsequential things and instead spent that time learning, I could greatly impact my life with new knowledge.  Instead of reading about some politician's errant tweets on CNN, I could be learning about [hydrodynamics](http://ocw.mit.edu/courses/mechanical-engineering/2-016-hydrodynamics-13-012-fall-2005/).  Well, maybe.

Reading the news everyday does not make me a better human. It does not make me a happier one, either.  The news is depressing, and it is that way by design.  We like reading about homicides, thefts, wars, and riots; if we did not, they would not print it.  Aaron continues:

> The news’s obsession with having a little bit of information on a wide variety of subjects means that it actually gets most of those subjects wrong. (One need only read the blatant errors reported in the corrections page to get some sense of the more thorough-going errors that must lie beneath them. And, indeed, anyone who has ever been in the news will tell you that the news always gets the story wrong.) Its obsession with the criminal and the deviant makes us less trusting people. Its obsession with the hurry of the day-to-day makes us less reflective thinkers. Its obsession with surfaces makes us shallow.

Subjecting myself to hours of tiny morsels of psuedo-facts dipped in sociopathic hollandaise is no way to live.  I am not saying all news is bad news, but after thinking about this for a while, it is obvious I could cut back. The news does not help my creativity, happiness, health, wellness, or mental stability.  Instead of being a passive activity, confronting the news should have a time and place.

Enter [NoNews](http://www.nonews.info) - the browser extension to save my (and your) happiness.

I've flirted with the idea of creating a browser extension for Google Chrome for quite some time.  After realizing my new found desire to avoid reading news sites would directly clash with my lack of will power, I decided to do what any lazy programmer would do: make some software to do it for me!

Once installed, NoNews filters the sites visited by your browser, and issues a warning page whenever you attempt to view a news site.  For me, passive browsing has introduced me to countless depressing stories that have little direct impact on my life - I just click, read, rinse, and repeat.  NoNews is designed to break this vicious cycle, reminding the user that this behavior is potentially harmful.

Recognizing that not all news is bad news, or unavoidable, I did include a couple of overrides.  First of all, every warning page has the ability to continue on to the news source using a one time pass.  The other option is the [snooze bar](http://www.nonews.info/how-to-snooze.html), which disables the NoNews filter for 15 minutes, allowing unbridled access to all the news that is news.

![Nonews Blocked Site Screen](/img/nonews-blocked-site-screen-800.jpg)
<h5 class="centered-text">Screenshot of blocked URL with red bypass button and snooze bar popup open.</h5>

Just like the news, NoNews is free.  Unlike the news, I hope it makes you happy.  You can get [NoNews in the Google Chrome Web Store](https://chrome.google.com/webstore/detail/no-news-is-good-news/fnikidjfogfllkinoahanihoddalbhil) (free), or check out the [source code on GitHub](https://github.com/JDStraughan/nonews).  If you are a developer, stay tuned, the next blog post is (planned to be) all about the NoNews source code.

For what it's worth, I can leave you with a final adage: [I'm eating my own dog food](http://en.wikipedia.org/wiki/Eating_your_own_dog_food).  For the past couple of weeks I have been leaving NoNews activated in my browser while I have been developing it on the side. I really expected this project to be a learning experience in building browser extensions, and not really yield a product I would use.  I picked the news blocker as a way to create something small, open source, and meaningful as a tribute to someone who contributed so much to the online world I sometimes take for granted.  What I have found is that using NoNews actually works for me, and I plan to leave it on and installed.  I now pick and choose my news more carefully, and limit the amount of time I spend reading about the latest war, riot, or scandal.  I think it has contributed to my overall happiness, productivity, and focus - I hope it does for you also.