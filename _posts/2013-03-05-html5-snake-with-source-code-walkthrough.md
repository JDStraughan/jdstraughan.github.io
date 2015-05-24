---
layout: post
published: true
title: HTML5 Snake source code walkthrough
tags: ["html5", "javascript", "game development"]
description: "Learn how to make a version of the popular snake game using the HTML canvas element and javascript. Fun for all ages, and easy to code."
header-img: 'img/header/lightcycles.png'
---

> *Thank you [HackerNews](http://news.ycombinator.com/)* for the *front-page* love!

<hr>

Last month I made my first game: [html5-lightcycles](https://github.com/JDStraughan/html5-lightcycles).  It is a Tron inspired lightcycle game, a humble recreation of the 1980s arcade classic. I wrote a blog post about the experience, [you can check it out here](http://jdstraughan.com/2013/02/14/my-first-game-html5-lightcycles/).  The whole truth behind the lightcycle game includes the tidbit that when I first started playing around with the creating that game, snake was my original intention.

For those that don't know it, snake is one of the oldest video games, with origins in the 1970s.  Nokia put it on cell phones in 1998, and it became a staple of cell phone gaming.  The concept is simple: you control a snake that grows when it consumes food, and dies when it hits a wall or itself.  Some variants include obstacles, increased speed, and other mechanisms to increase difficulty.

Moving a box across the canvas was easy, and turning off the screen reset gave me lightcycles for free.  Getting the snake to turn corners was not as easy as lightcycles, so I abandoned snake and moved on, eventually creating several proof of concept game-like creations.  I thought about snake again, and it hit me: the snake is an array, and shift()ing and push()ing coordinates would let it move properly, and when it got food, it could just skip the <code>shift()</code>.  The rest just came together.

## The Game

If you are visiting this page in an HTML5 capable browser, and are not on a mobile device, then this game should work for you.

<iframe style="width: 100%; height: 480px" src="http://jsfiddle.net/zT7V4/13/embedded/result/" allowfullscreen="allowfullscreen" frameborder="0"></iframe>

You can also view/play the game (in 640x480 mode) directly on [jsfiddle](http://jsfiddle.net/zT7V4/9/embedded/result/).

## The Source Code

For those that are interested, here is a quick overview of the code for this game.

First, we need a basic page setup with a canvas tag.

{% highlight html %}

<!DOCTYPE html>
<html>
<head>
<meta charset="utf-8" />
<title>HTML5 snake - Canvas Snake Game</title>
<!--[if IE]>
<script src="http://html5shiv.googlecode.com/svn/trunk/html5.js"></script>
<![endif]-->
<link rel="stylesheet" href="page.css" type="text/css">
</head>
<body>

<h1>html5-snake</h1>
<p><a href="http://en.wikipedia.org/wiki/HTML5">HTML5</a> variation of the classic <a href="http://en.wikipedia.org/wiki/Snake_(video_game)">snake game</a>.</p>

<div>
  <canvas id="the-game" width="320" height="240">
</div>

<p>Control snake with arrow keys, WASD, or HJKL (vim keys).</p>

<p>New food may appear under snake, uncoil to reveal.</p>

<p>Collect the food to grow and increase speed.</p>

<p>&copy; 2013 - <a href="http://JDStraughan.com">JDStraughan.com</a> - <a href="https://github.com/JDStraughan/html5-snake">Source on GitHub</a></p>
<script type="text/javascript" src="game.js"></script>
</body>
</html>

{% endhighlight %}

Nothing special here, just a standard page, canvas element, and some basic instructions and description of game play.

{% highlight css %}

article, aside, details, figcaption, figure, footer, header,
hgroup, menu, nav, section {
  display: block;
}
body {
  background-color: #CCC;
}
h1 {
  text-align: center;
}
p {
  text-align: center;
}
canvas {
  display: block;
  margin: 0 auto;
  background-color: #666;
}

{% endhighlight %}

Again, very simple.  You could really skip all of this and just drop a canvas element on a page, but some basic styling and semantic markup goes a long way.

HTML5 canvas games are javascript, like Soylent Green is people.

In this game, I have one javascript file to rule them all: <code>game.js</code>.

To make the code more bearable, I have organized the file into the following groups:

* game (object)
* snake (object)
* food (object)
* some helpers/service methods
* an event listener
* the game loop

Well, before all of that, we do have to grab the canvas context like so:

{% highlight javascript %}

var canvas = document.getElementById("the-game");
var context = canvas.getContext("2d");

{% endhighlight %}

Now that we have that out of the way, lets take a walk thru something more fun.

####Game Object

The <code>game</code> object contains controls for score, game state, and some display.

{% highlight javascript %}

game = {

  score: 0,
  fps: 8,
  over: false,
  message: null,

  start: function() {
    game.over = false;
    game.message = null;
    game.score = 0;
    game.fps = 8;
    snake.init();
    food.set();
  },

  stop: function() {
    game.over = true;
    game.message = 'GAME OVER - PRESS SPACEBAR';
  },

  drawBox: function(x, y, size, color) {
    context.fillStyle = color;
    context.beginPath();
    context.moveTo(x - (size / 2), y - (size / 2));
    context.lineTo(x + (size / 2), y - (size / 2));
    context.lineTo(x + (size / 2), y + (size / 2));
    context.lineTo(x - (size / 2), y + (size / 2));
    context.closePath();
    context.fill();
  },

  drawScore: function() {
    context.fillStyle = '#999';
    context.font = (canvas.height) + 'px Impact, sans-serif';
    context.textAlign = 'center';
    context.fillText(game.score, canvas.width/2, canvas.height  * .9);
  },

  drawMessage: function() {
    if (game.message !== null) {
      context.fillStyle = '#00F';
      context.strokeStyle = '#FFF';
      context.font = (canvas.height / 10) + 'px Impact';
      context.textAlign = 'center';
      context.fillText(game.message, canvas.width/2, canvas.height/2);
      context.strokeText(game.message, canvas.width/2, canvas.height/2);
    }
  },

  resetCanvas: function() {
    context.clearRect(0, 0, canvas.width, canvas.height);
  }

};

{% endhighlight %}

Most of the property names should be self explanatory:

* <code>game.score</code> keeps the current score of the game, initially 0
* <code>game.fps</code> stores the _frames per second_ (fps) the game is running at.  This increases with difficulty.
* <code>game.over</code> starts as true.  It just sounds right when checking for game state.
* <code>game.message</code> holds any messages to display to the user, for now there is only the "game over" message.

The <code>game.start()</code> method is pretty straightforward: it sets the game state via <code>game.over = false</code> and clears the <code>game.message</code> and <code>game.score</code>, resets the fps, and then runs an <code>init()</code> method on the <code>snake</code> object.  We'll see what that does in a second, but we can assume it initializes the snake "game piece".  The same goes for <code>food.set()</code>.

The <code>game.stop()</code> method sets the game state to _over_ and displays a friendly message explaining how to start a new game.

The next three methods draw things for the game: a box, a score, and a message.  The box method is used to draw both food and snake parts, as a snake is just a line of boxes.  The <code>game.drawBox()</code> method takes an x and y coordinate, a size, and a color.  With these parameters, it can go to the x,y coordinates specified, and draw a box <code>size</code> high and wide, and fill it with the <code>color</code> desired.

<code>drawScore()</code> is a bit different, as it displays the score almost full hight of the canvas, with a color very close to the background color.  This allows the score to be shown without having to sacrifice game board real-estate for the sole purpose of the score.

Our <code>game.drawMessage()</code> method also is nothing special, it checks if a <code>game.message</code> exists, and puts in on the canvas if so.

The last method in the <code>game</code> object is <code>resetCanvas()</code>.  This clears the canvas in between draws to allow the score and messages to be displayed properly, and wiped when needed.

The <code>game</code> object sets up a lot of the underpinning for our game, but the real challenge for me, and the coolest part of this game in my opinion, is the <code>snake</code> object.

####Snake Object

While it's no Mario, our snake is our hero.

{% highlight javascript %}

snake = {

  size: canvas.width / 40,
  x: null,
  y: null,
  color: '#0F0',
  direction: 'left',
  sections: [],

  init: function() {
    snake.sections = [];
    snake.direction = 'left';
    snake.x = canvas.width / 2 + snake.size / 2;
    snake.y = canvas.height /2 + snake.size / 2;
    for (i = snake.x + (5 * snake.size); i >= snake.x; i-=snake.size) {
      snake.sections.push(i + ',' + snake.y);
    }
  },

  move: function() {
    switch(snake.direction) {
      case 'up':
        snake.y-=snake.size;
        break;
      case 'down':
        snake.y+=snake.size;
        break;
      case 'left':
        snake.x-=snake.size;
        break;
      case 'right':
        snake.x+=snake.size;
        break;
    }
    snake.checkCollision();
    snake.checkGrowth();
    snake.sections.push(snake.x + ',' + snake.y);
  },

  draw: function() {
    for (i = 0; i < snake.sections.length; i++) {
      snake.drawSection(snake.sections[i].split(','));
    }
  },

  drawSection: function(section) {
    game.drawBox(parseInt(section[0]), parseInt(section[1]), snake.size, snake.color);
  },

  checkCollision: function() {
    if (snake.isCollision(snake.x, snake.y) === true) {
      game.stop();
    }
  },

  isCollision: function(x, y) {
    if (x < snake.size/2 ||
        x > canvas.width ||
        y < snake.size/2 ||
        y > canvas.height ||
        snake.sections.indexOf(x+','+y) >= 0) {
      return true;
    }
  },

  checkGrowth: function() {
    if (snake.x == food.x && snake.y == food.y) {
      game.score++;
      if (game.score % 5 == 0 && game.fps < 60) {
        game.fps++;
      }
      food.set();
    } else {
      snake.sections.shift();
    }
  }

};

{% endhighlight %}

This object is significantly larger than the last one, and has a lot more coolness, also. The first thing the class does is initiate the <code>snake.size</code> property, and we notice it is dynamic: <code>size: canvas.width / 40</code>.

This game was designed to run on either a <code>320x240</code> or <code>640x480</code> canvas.  This means our <code>snake.size</code> is directly proportional to our canvas, and at our predetermined canvas sizes, we'd end up with the following section sizes:

> 320 / 40 = 8

> 640 / 40 = 16

Note that here size refers to each section of the snake, so on a <code>320x240</code> canvas, our snake will be made up of a bunch of 8x8 pixel squares.  Next we initialize the x and y coordinates for the snake, and set the default starting <code>direction</code>.  Lastly, and most importantly, is the <code>snake.sections</code> array.

Unlike many game pieces, our snake does not just move, it crawls.  As its head moves forward, its body continues to follow the same path, and turning a corner can take several "moves".  To accomplish this, our snake is make up of an array of coordinates, and we will <code>shift()</code> and <code>push()</code> coordinates off and on to the array to update it's position.

Creating a new snake has a few parts, so there is an <code>init()</code> method to take care of getting it constructed. This method starts by ensuring the <code>sections</code> array is cleared and the starting <code>direction</code> is reset. It then places the snake in the middle of the game board, and again avoids hard-coded positions in favor of allowing for multiple canvas sizes.

{% highlight javascript %}

snake.x = canvas.width / 2 + snake.size / 2;
snake.y = canvas.height /2 + snake.size / 2;

{% endhighlight %}

We need to remember that when <code>game.drawBox()</code> creates each section, it begins from the x,y coordinates, then creates a box based on the height and width with the x,y in the center.  This means that a box will have a center that is 1/2 the width and 1/2 the height.  To abide by these conditions, our snake is positioned accordingly.

Lastly, our <code>init()</code> method builds our starting snake:

{% highlight javascript %}

for (i = snake.x + (5 * snake.size); i >= snake.x; i-=snake.size) {
  snake.sections.push(i + ',' + snake.y);
}

{% endhighlight %}

This creates 5 array elements, each containing an x,y coordinate that is offset by the <code>snake.size</code>.  Note that we are beginning the array with the tail, and ending it with the head. This will make <code>shift()</code> remove the tail, and we can use <code>push()</code> to advance the head position.  The <code>snake.x</code> and <code>snake.y</code> coordinates always refer to the head position.

Next we have the <code>snake.move()</code> method. This checks the <code>snake.direction</code>, which is updated later in the script, and then manipulates the head of the snake accordingly.  Upward movement requires, the y coordinate to decrease, down increases, left decreases the x coordinate, and inversely, right increases it.  Notice the unit added or removed is <code>snake.size</code>, since the snake sections sizes are larger than 1px we need to move the head by the size of the sections.

After updating the head position, <code>snake.move()</code> then invokes <code>snake.checkCollision()</code>.  We'll cover this method in a minute, just know it determines if the head as collided with the outside of the game area, or with an existing section of the snake.  Either of these results in the game being stopped.

Next our <code>move()</code> method continues on to the <code>snake.checkGrowth()</code> method, which will allow the snake to grow if it is on the same position as a piece of food. Like <code>snake.checkCollision()</code>, we'll cover the internals of this function in more detail shortly.

The last thing <code>move()</code> does is <code>push()</code> the new <code>snake.x</code> and <code>snake.y</code> coordinates onto the <code>snake.sections</code> array.

Our next method, <code>snake.draw()</code>, iterates through the <code>snake.sections</code> array and then splits the values into arrays with the x and y coordinates, and passes them along to the next method in the class: <code>snake.drawSection()</code>.

Here we circle back to the game.drawBox() function, and pass the x, y, size, and color of our snake.  In turn, each section is drawn, and our array can be represented as a series of boxes on our canvas.

Moving on through the <code>snake</code> object, we see the <code>checkCollision()</code> and <code>isCollision()</code> functions.  The first, <code>checkCollision()</code>, is really just a helper that calls <code>isCollision()</code> on our snake's coordinates, and stops the game if there are any collisions found.

Taking a look at <code>snake.isCollision()</code>, we find that determining if our snake if out of bounds or being cannibalistic is not very difficult:

{% highlight javascript %}
  ...
  isCollision: function(x, y) {
    if (x < snake.size/2 ||
        x > canvas.width ||
        y < snake.size/2 ||
        y > canvas.height ||
        snake.sections.indexOf(x+','+y) >= 0) {
      return true;
    }
  },
  ...
{% endhighlight %}

The first four tests determine if the head of our snake has moved to a position that is outside the boundaries of our canvas. Again we are reminded to check for half of the size of the snake when looking for boundaries.  If all those checks pass, we then see if the snake has attempted to eat itself by seeing if the coordinates exist in the <code>snake.sections</code> array.

The last method in our <code>snake</code> class is there to see if our snake needs to grow.

{% highlight javascript %}
  ...
  checkGrowth: function() {
    if (snake.x == food.x && snake.y == food.y) {
      game.score++;
      if (game.score % 5 == 0 && game.fps < 60) {
        game.fps++;
      }
      food.set();
    } else {
      snake.sections.shift();
    }
  }
  ...
{% endhighlight %}

I could have probably named this method better, and future versions may see this refactored out into some more granular functions for readability and maintainability.  For now, we'll dissect it as it is.  The method starts off by determining if the head position of the snake is the same as the position of the food, the proverbial mushroom for our hero snake.  If the head is on a food piece the function increases the <code>game.score</code>, calls <code>food.set</code>, and if the score is divisible by 5 it increases the the game speed by 1 FPS.  If the snake is not on a food piece, then it <code>shifts()</code> off the tail section.  Notice if the snake eats food, the <code>shift()</code> never happens. This is how our snake grows.

####Food object

Our food object is short and sweet.

{% highlight javascript %}

food = {

  size: null,
  x: null,
  y: null,
  color: '#0FF',

  set: function() {
    food.size = snake.size;
    food.x = (Math.ceil(Math.random() * 10) * snake.size * 4) - snake.size / 2;
    food.y = (Math.ceil(Math.random() * 10) * snake.size * 3) - snake.size / 2;
  },

  draw: function() {
    game.drawBox(food.x, food.y, food.size, food.color);
  }

};

{% endhighlight %}

Like the snake object, <code>food</code> must have a <code>size</code>, <code>x</code>, <code>y</code>, and <code>color</code>. The real magic in this object is in the <code>set</code> method.

When <code>food.set</code> is called, the first thing that happens is the size of the food is set to match the size of the snake.  Next, we set this x and y coordinates for randomized food placement.  Because our snake really moves around in a grid that is determined by the width of each section, not every pixel on the canvas is accessible by the center of the snake's head.  To place food in a position where can be consumed, some care must be given.

{% highlight javascript %}

food.x = (Math.ceil(Math.random() * 10) * snake.size * 4) - snake.size / 2;
food.y = (Math.ceil(Math.random() * 10) * snake.size * 3) - snake.size / 2;

{% endhighlight %}

We can determine that <code>Math.ceil(Math.random() * 10</code> will return an integer between 1 and 10, and we can multiply that by <code>snake.size</code> and the ratio it has in relation to the overall canvas.  To offset for the center of the snake head, we'll need to subtract the width, <code>snake.size / 2</code>.  This looks complex, but it really just finds an x,y placement that is inside the canvas and accessible to our snake.

The last method in our <code>food</code> class is <code>draw()</code>.  Predictably, this simply calls the <code>game.drawBox()</code> method, and passes along all the food parameters.

####Helpers

To make this game work, we need to be able to utilize a few helpers.

{% highlight javascript %}

inverseDirection = {
  'up':'down',
  'left':'right',
  'right':'left',
  'down':'up'
};

keys = {
  up: [38, 75, 87],
  down: [40, 74, 83],
  left: [37, 65, 72],
  right: [39, 68, 76],
  start_game: [13, 32]
};

Object.prototype.getKey = function(value){
  for(var key in this){
    if(this[key] instanceof Array && this[key].indexOf(value) >= 0){
      return key;
    }
  }
  return null;
};

{% endhighlight %}

We cannot allow our snake to move the opposite direction it is currently moving, and having a quick place to lookup the inverse of a direction is helpful, so we have a <code>inverseDirection</code> object where this can be accomplished easily.

We want our users to be able to use multiple sets of keys to control our snake, so we have a <code>keys</code> object literal that defines our four directions with arrays of [char codes](http://www.cambiaresearch.com/articles/15/javascript-char-codes-key-codes) for the respective keys.  We'll be accepting WASD, arrow keys, and the vim movement keys HJKL.

The last of our helpers adds a <code>getKey()</code> method to the Object prototype so we can see if a key code is present in our <code>keys</code> object, and if so return the key of the matched value. So really all it does it return a direction based on a key's char code.  This makes our life a lot easier when we need to see if a key represents a direction.

We're on our way to a complete game.  All that is left is a way to listen for new input, and the infamous game loop.

####Event Listener

Javascript makes it very easy to listen for new inputs from the keyboard.

{% highlight javascript %}

addEventListener("keydown", function (e) {
    lastKey = keys.getKey(e.keyCode);
    if (['up', 'down', 'left', 'right'].indexOf(lastKey) >= 0
        && lastKey != inverseDirection[snake.direction]) {
      snake.direction = lastKey;
    } else if (['start_game'].indexOf(lastKey) >= 0 && game.over) {
      game.start();
    }
}, false);

{% endhighlight %}

Now when a key is pressed, our event listener will fire, and these few lines of code with execute.  Here we utilize our <code>getKey()</code> method to see if the key pressed was a movement key, and if so we also assert that it is not the <code>inverseDirection</code> of our current <code>snake.direction</code>.  If these conditions are met, the <code>snake.direction</code> is updated and our snake will be off on a new bearing.  If the key was not a direction key, the listener checks to see if it was a spacebar or enter key, and if it is the <code>game.start()</code> method is called.  If the key is none of these, the key-press is ignored.

That is pretty simple, and our helpers made the listener much easier to read and follow along with.

####Game loop

Here were are, the final stage.  We've done all the heavy lifting of creating our game environment, our player, objectives, scoring, difficulty increases and even the drawing of our assets.  Now we need to pull it all together and make it run.

{% highlight javascript %}

var requestAnimationFrame =  window.requestAnimationFrame ||
      window.webkitRequestAnimationFrame ||
      window.mozRequestAnimationFrame;

function loop() {
  if (game.over == false) {
    game.resetCanvas();
    game.drawScore();
    snake.move();
    food.draw();
    snake.draw();
    game.drawMessage();
  }
  setTimeout(function() {
    requestAnimationFrame(loop);
  }, 1000 / game.fps);
};

requestAnimationFrame(loop);

{% endhighlight %}

For our game loop, I am using the <code>requestAnimationFrame</code> method, which requires setting the appropriate, browser specific function.  For a detailed look at <code>requestAnimationFrame</code>, I recommend consulting [The Book of Mozilla](https://developer.mozilla.org/en-US/docs/DOM/window.requestAnimationFrame).

Once the <code>requestAnimationFrame</code> is set, it can be called to update our canvas and will keep our refresh rate at or below 60 FPS.  The <code>requestAnimationFrame</code> accepts a callback that is invoked no more than 60 times per second, and in our case we'll use it in a recursive <code>loop()</code> function.

Looking inside <code>loop()</code>, we see the first thing that happens a check for the games state.  If the game is active  (not over) then a series of instructions is executed. Let's review what each of these do:

* <code>game.resetCanvas()</code> - Clears the canvas of all drawings
* <code>game.drawScore()</code> - Draws the <code>game.score</code> into the background of our game board
* <code>snake.move()</code> - Calculates the new head position of our snake
* <code>food.draw()</code> - Places a piece of food on the game board
* <code>snake.draw()</code> - Draws our snake by iterating through the <code>snake.sections</code> array
* <code>game.drawMessage()</code> - Displays game messages on screen, used for GAME OVER message

By executing these functions multiple times a second, our snake is able to move, and our game becomes alive.  To do this, we'll create an infinite loop.

{% highlight javascript %}
  ...
  setTimeout(function() {
    requestAnimationFrame(loop);
  }, 1000 / game.fps);
  ...
{% endhighlight %}

No human can control our snake advancing 60 times per second, so to slow down animation we can throttle the <code>requestAnimationFrame</code> to only fire _n_ times per second by using javascript's native <code>setTimeout()</code> method.  Remember we initialize <code>game.fps</code> to 8, so <code>setTimeout()</code> will allow <code>requestAnimationFrame</code> to execute 8 times per second, and it will increase along with <code>game.fps</code> to make the snake move faster over time. There are other methods available for controlling game speed, but I like the way this give the snake that vintage gaming feel, with a bit of a jerking motion when as it slithers to its next meal.

The script in completed with one final line of code that sets our <code>loop()</code> function in motion:

{% highlight javascript %}

requestAnimationFrame(loop);

{% endhighlight %}

We now have a classic game recreated in javascript, in just a couple of hundred lines of code.  The source is [available on GitHub for your forking pleasure](https://github.com/JDStraughan/html5-snake).  For my second complete HTML5 game, I'm pretty happy and learned a lot about the canvas element, and game development in general.  I look forward to your questions and constructive criticism in the comments section below.
