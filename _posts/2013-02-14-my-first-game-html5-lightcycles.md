---
layout: post
published: true
title: My first game - HTML5 lightcycles
tags: ["html5", "javascript", "game development"]
description: "For the past few months I have been playing with the HTML5 canvas element off and on, eventually building TRON, my first canvas game."
header-img: "img/header/tron.jpg"
image-credit: Pacifier (Own work) [<a href="http://creativecommons.org/licenses/by-sa/3.0/">CC-BY-SA-3.0</a>], <a href="http://commons.wikimedia.org/wiki/File%3ATron_bryce.jpg">via Wikimedia Commons</a>
---

For the past few months I have been playing with the [HTML5 canvas element](https://en.wikipedia.org/wiki/Canvas_element) off and on.  In recent years it has been the subject of much excitement in the web development community, and as matured rapidly in modern browsers.  Now there are a wide variety of games and activities that utilize this relatively new element.

For whatever reasons, I just assumed manipulating the canvas would be a pain in the ass, so I avoided it.  I thought, "I am not a game developer, so I have no need for it."  I understood that not all canvas use was in the realm of game development, however, the other use cases weren't inspiring me to learn.

So I ignored the canvas.

Until a few months ago.  I was reading a thread on reddit and commenting to a new web developer about how learning to program is a process of writing code that does not work properly, finding the issues, fixing them, and then moving on.  In this same thread, someone had commented that when they get bored learning a particular programming lesson, they just fire up [jsfiddle](http://jsfiddle.net/) and play with the canvas - because, they said - "it is just so fun and simple".

Fun and simple? Canvas?

"Canvas is simple and fun", they said. "Reddit is full of trolls", I said. I don't like to feed the trolls.  If it was simple, everyone would be doing it.  If it were simple, then even I could write a game in javascript. So like any good redditor, I fired up my browser and was going to prove to the world that this cannot be, in any way, simple.

A few minutes later I had a square drawn on my screen.  A few hours later I had multiple shapes in different colors.  And they were moving.  By the end of that first day, I had accomplished no real work, but I had shapes moving on my screen, under my control.

My mind exploded with ideas.  Every mirror I passed had a cheerful Gabe Newell staring back at me.  It had been over twenty years since I thought about writing games when I grew up.  I have written code all my life, but somewhere along the way I lost the dream of my childhood.  I had never written a game.

Unfortunately, I have a job.  Where I write code.  I can't really complain, I like writing code, but everyday my mind was aching to get back to the canvas.  Although I have not been able to devote large swaths of time to this endeavor, a few hours here and there is all that is needed to learn the basics required to get a game going in HTML5.  I'd draw shapes, make them spin, move, wrap, warp, and collide.

I had finally done it - I was writing a game.  Several, in fact. I had started a snake game, an asteroids game, and a math puzzle game.  I was like Peter Pan.  I tinkered with each, wrote and replaced a lot of bad code, contemplated how to determine if my shape collided with another, read stack overflow, blogs, and docs.

A few days ago I decided to lower the bar, and just sit down and complete a game.  Something that could be played, had a beginning and an end, and was fun.  Thinking about what I wanted to build, I started with a small blue box and started moving it around the canvas.  Then I commented out the code that redraws the board several times a second and kept "playing".  As you can guess, the box did not just move anymore, it just "expanded" in one direction.  Like the lightcycles from Tron.

I was 7 (the same age my son is now) when Tron came to the movie theater, and it changed my life.  Programs, users, computers, graphics, gaming: Tron had it all.  It was also one of the first VHS movies we had, and I am sure I just about wore the tape out.  Once the arcade game was released, like many kids in the 80s, I dropped every quarter I could find into it.  Of all the games it offered, I loved the lightcycles.  I fought for the Users.

<div class="video">
<iframe width="640" height="480" src="http://www.youtube.com/embed/ONg0rUogiEg?rel=0" frameborder="0" allowfullscreen></iframe>
<h5>Original Tron arcade game - lightcycle level</h5>
</div>

I had my inspiration.  I'd build a lightcycle game.

For the uninitiated, like myself, the logic behind gaming is a mystery.  I have contemplated it often, but in all honesty have not read much on the subject.

Based on my previous experiments, reading lots of blog posts (I wish I could list them all here) and a fair amount of generous stackoverflow answers, I could make my cycle appear, move, and I had figured out how to detect if I had left the game grid.

The canvas, after all, is just a bitmap with a simple API.  Like an etch-a-sketch.  You can move to a location, draw, fill, relocate, etc.  Because this etch-a-sketch is in a computer, you can refresh it so many times a second no one will ever be the wiser.  That is how animation works.

The tutorials, blogs, and answers got me that far.  Beyond this was still covered in a cloud of mystery.  In my snake game I had not crossed the issue of the snake colliding with itself, and randomizing the location of the bait after each consumption was pretty easy. While this attempt at a game has not found completion, it was the early sandbox that I used to work with.  This lightcycle game was going to require an enemy, not just a piece of bait.  This enemy would need to have some intelligence, the game would need to know when there was a collision, and it would need to determine a winner.  These all seemed like daunting tasks.

I thought about studying up on some game development, finding a good framework, and putting together a game.  Someday I may do this, but I decided to stick with native js, learn the ropes, see what is happening under the hood.  I'd just try to solve some of these problems and then go see how others do it.  For the lightcycle's walls, for example, I just made a <code>history</code> array withe coordinates of each movement.  Each time a cycle moves, it checks that the new coordinates are not outside the map, or in the history of either cycle.  Why did I not just lump them all into one array?  Who knows, I was too busy trying to figure out how to make it work.  Is there a better way?  I sure hope so, but this was working so I moved on.

>Note: A redditor pointed out it would be better to have an game board 2D array that stores the x,y coords of used tiles and instead of checking <code>indexOf</code> for each cycle's history, I can just look for existence of the x,y in the array. I'll add this in the next iteration of the game. Thanks _dmcinnes_!

Soon I had collisions, movement, light.  I felt close to completion.  Then I realized I had to create some way to control the enemy cycle, and it was not going to write itself.  For my first attempt, I simply had the enemy cycle start each turn looking right, and if it could go right it did.  Then it looked up, then left, then down.  It was not the smartest cycle in the world, but it gave me something to play against while testing my other code.

In my second attempt at "AI" (I use that term very loosely), I decided to create an advisor method.  This function would scan the 4 directions, determine the best one, and advise the cycle of the direction with the most runway.  If the current direction was blocked in the next tile, it would turn using the best route.  To keep the player on their toes, it also has a ~10% chance of turning to the advisor's best option on each turn.

Not the most elegant approach, I admit, but it works.  As of the time of this writing, it is what is currently controlling the Program bike. This method does beat me more often than I'd care to admit.  Of all the areas I'd like to spend more time perfecting, this is the most intriguing to me.

Overall, I am quite happy that for under 250 lines of javascript I was able to produce a playable game that is both fun and challenging.  If you are looking for a simple and fun way to expand your programming chops, I highly recommend trying out the canvas element.

You can play my HTML5 lightcycle game [in 640x480 mode here](http://jsfiddle.net/PxpVr/17/embedded/result/), or [in 320x240 mode here](http://jsfiddle.net/PxpVr/16/embedded/result/). As always, source code is [available on GitHub](https://github.com/JDStraughan/html5-lightcycles).
