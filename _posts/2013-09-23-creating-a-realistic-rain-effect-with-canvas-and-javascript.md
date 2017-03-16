---
ID: 998
post_title: >
  Creating a Realistic Rain Effect with
  Canvas and JavaScript
author: admin
post_date: 2013-09-23 11:06:27
post_excerpt: ""
layout: post
permalink: >
  http://modern-web.dev/2013/09/23/creating-a-realistic-rain-effect-with-canvas-and-javascript/
published: true
dsq_thread_id:
  - "2809890575"
om_counter:
  - "672"
---
*By [Marek Brodziak][1]* I recently released a fun project called [rainyday.js][2]. I consider it a rather humble piece of code and, in fact, it is more or less my first contact with JavaScript on a level that requires a little more than displaying alert popups. Hopefully, some of you will find it useful and interesting. The idea behind rainyday.js is to create a light JavaScript library that makes use of the HTML5 canvas to render an animation of raindrops falling on a glass surface. Simple enough, yet challenging at times, especially since we're trying to avoid that cartoonish look usually attributed to JavaScript animations and at the same time make sure the animations work smoothly. In this article I'll try to describe the overall approach as well as the "little things", so bare with me. But first, you might want to check out the following demos I've prepared, to give you an idea of what I'm actually talking about (click the images to open the demo on JSBin): <a href="http://jsbin.com/EjoGeLo/2" target="_blank"><img class="alignnone size-full wp-image-999" alt="rainydaydemo1" src="http://flippinawesome.org/wp-content/uploads/2013/09/rainydaydemo1.png" width="702" height="386" /></a> <a href="http://jsbin.com/eDALEfE/2" target="_blank"><img class="alignnone size-full wp-image-1000" alt="rainydaydemo2" src="http://flippinawesome.org/wp-content/uploads/2013/09/rainydaydemo2.png" width="702" height="386" /></a> 
## Preparing Canvas Layers The animation uses three different canvas objects in order to achieve the layering effect, as can be seen on the figure below. 

[<img class="alignnone size-full wp-image-1001" alt="rainyday_layers" src="http://flippinawesome.org/wp-content/uploads/2013/09/rainyday_layers.png" width="1023" height="726" />][3] The bottom layer contains the original image, scaled to the size requested via the API. The contents of the image are simply drawn on the canvas and afterwards the image is blurred so that it appears to be out of focus. The current implementation makes use of the [Stack Blur Algorithm][4] by Mario Klingemann. The middle layer is basically an invisible canvas. It is not a part of the DOM tree, but instead, serves as a helper when rendering raindrop reflections. The original image is rotated and flipped accordingly, which makes the entire process easier later on. Finally the top layer (the `glass`) is the one we use for drawing raindrops. It is positioned directly above the first canvas. The entire concept is pretty simple and easy to understand. Having three canvas instances instead of one makes the script code much simpler in terms of readability and significantly improves performance. 
## A Closer look at the Raindrops Rendering raindrops is one of the most important aspects of rainyday.js. While this is a relatively simple task for small drops, in case of the larger ones there are a number of factors that need to be considered. For example, we need to make sure the shape of the drops are properly randomized. In order to achieve that, rainyday.js uses an algorithm to approximate the shape of a circle (see 

<a href="http://rectangleworld.com/blog/archives/413" target="_blank">this cool article</a> on imperfect circles by Dan Gries) with a couple of tweaks. As a result, the shapes rendered by the script look something like this: [<img class="alignnone size-full wp-image-1002" alt="rainyday_drops" src="http://flippinawesome.org/wp-content/uploads/2013/09/rainyday_drops.png" width="722" height="497" />][5] The output of the algorithm is a linked list of points determined along the circle line. Drawing the raindrop is as simple as connecting them together while slightly randomizing the radius along the way, and clipping whatever we choose as the reflection to the final shape. 
## Running the animation Finally, the last piece of the puzzle is the animation "engine", which consists of three modules. Each one of them depends on the previous one and the output is what you can see in the demos at the top of this article. The modules are: 

1.  **The rain module** - a JavaScript timing event scheduled whenever the `rain()` method is invoked. Using the time-interval specified by the user, this module places new drops at random positions on the canvas. The size of a new drop is determined by the presets, which allow users to control the amount and size of raindrops within the animation. Using an example from one of the demos, the following invocation of the `rain()` method will result in a new raindrop added to the canvas every `100ms`. Size of those drops will be distributed as follows: `88%` between `3 and 6` (minimum of 3 + a random value between 0 and 3), `2% (0.9 - 0.88)` of size `5` and the remaining `10% (1 - 0.9)` between `6 and 8`: 
        engine.rain([
            engine.preset(3, 3, 0.88),
            engine.preset(5, 5, 0.9),
            engine.preset(6, 2, 1),
        ], 100);

2.  **The gravity module** - once a raindrop is added to the animation, an additional timing event is scheduled to control the drop movement on the canvas by modifying its position on the `Y` axis (and on the `X` axis as well for that matter, in order to simulate rain falling from a given angle). At the moment rainyday.js delivers two different implementations of the gravity function: `GRAVITY_LINEAR` (simple gravity with constant acceleration) and `GRAVITY_NON_LINEAR` (a little more random movement of the drops). Selecting a gravity function is as simple as calling: 
        engine.gravity = engine.GRAVITY_NON_LINEAR;

3.  **The trail module** - a function executed after each gravity movement in order to render a trail behind a falling drop. The `TRAIL_DROPS` function implements a trail of smaller drops, while `TRAIL_NONE` disables the trailing. Selecting a trailing function consists of calling: 
        engine.trail = engine.TRAIL_DROPS;

## Where to Go From Here The source code of rainyday.js is available on 

[GitHub][2], so if you've enjoyed it, go and have a look. For the next release (this article has been prepared based on `0.1.1`) I'll be working on implementing collision detection between raindrops as well as some other minor improvements I have in mind. Any comments, suggestions and feedback (of any sort really) are very welcome.

 [1]: /authors/marek-brodziak
 [2]: https://github.com/maroslaw/rainyday.js
 [3]: http://flippinawesome.org/wp-content/uploads/2013/09/rainyday_layers.png
 [4]: http://www.quasimondo.com/StackBlurForCanvas/StackBlurDemo.html
 [5]: http://flippinawesome.org/wp-content/uploads/2013/09/rainyday_drops.png