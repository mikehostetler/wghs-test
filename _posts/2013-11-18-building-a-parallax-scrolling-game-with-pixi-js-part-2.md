---
ID: 1269
post_title: >
  Building a Parallax Scrolling Game with
  Pixi.js – Part 2
author: admin
post_date: 2013-11-18 12:17:25
post_excerpt: ""
layout: post
permalink: >
  http://modern-web.dev/2013/11/18/building-a-parallax-scrolling-game-with-pixi-js-part-2/
published: true
dsq_thread_id:
  - "2809980340"
om_counter:
  - "43"
---
*By [Christopher Caleb][1]* In this series of tutorials we'll discover how to make a parallax scrolling map similar to that found in games such as [Canabalt][2] and [Monster Dash][3]. The <a href="http://flippinawesome.org/2013/11/04/building-a-parallax-scrolling-game-with-pixi-js/" target="_blank">first part</a> introduced the [pixi.js][4] rendering engine and covered the fundamentals of parallax scrolling. Now we'll build upon our first scrolling attempt by adding the concept of a viewport. 
### What you will learn…

*   How to extend pixi.js display objects
*   The basics of object-oriented JavaScript
*   How to add a viewport to your scroller

### What you should know…

*   An awareness of object-oriented concepts
*   Some pixi.js fundamentals You'll be working from the code you produced during the 

<a href="http://flippinawesome.org/2013/11/04/building-a-parallax-scrolling-game-with-pixi-js/" target="_blank">first tutorial</a>. Alternatively you can <a href="https://github.com/ccaleb/pixi-parallax-scroller/tree/master/tutorial-1" target="_blank">download the previous tutorial's source</a> from GitHub and work from there. Additionally, the <a href="https://github.com/ccaleb/pixi-parallax-scroller/tree/master/tutorial-2" target="_blank">entire source for this second tutorial</a> is also available on GitHub. [<img class="alignnone size-full wp-image-1204" alt="pixi-parallax-demo" src="http://flippinawesome.org/wp-content/uploads/2013/11/pixi-parallax-demo.png" width="512" height="384" />][5] As a reminder, click on the image above. It will launch and run the current version of our parallax scroller. There are only two layers at the moment but we'll start adding a third, more sophisticated layer, in the next tutorial. In the meantime we'll put ourselves in a position to add that third layer by adding the concept of a viewport. While we work, we'll also perform some significant re-factoring in order to wrap our scroller within its own class. While this tutorial is very much aimed at a beginner's level, it does expect that you have at least a fairly basic understanding of object-oriented programming concepts. Don't worry if that statement makes you feel a little uncomfortable, as I'll still provide enough gentle prodding in the right direction for those who are unfamiliar with object-oriented JavaScript. 
## Getting Started If you haven't worked your way through the 

[first tutorial][6] in the series then I recommend you start from there. It's also worth remembering that you'll need to be running a local web server in order to test your work. If you haven't already done so, refer to the [first tutorial's Getting Started section][6] for details regarding how to set up your web server. 
## Extending Pixi Display Objects As we discovered previously, pixi.js provides several display object types that you can work with. If you remember, we briefly played with 

`PIXI.Sprite` before settling on `PIXI.TilingSprite` for our needs. Both classes share a lot of common functionality. For example, they both provide you with position, width, height, and alpha properties. Also, both can easily be added to the stage via its `addChild()` method. In fact, the stage (`PIXI.Stage`) itself is a display object and also provides you with many of the same properties used by both the `Sprite` and `TilingSprite` classes. All this common functionality is available thanks to the wonders of inheritance, where one class can inherit and extend the features of another. To help you understand this take a look at the diagram below, which shows the majority of display objects provided by pixi.js. [<img class="alignnone size-full wp-image-1270" alt="ps-tut2-screenshot1" src="http://flippinawesome.org/wp-content/uploads/2013/11/ps-tut2-screenshot1.png" width="432" height="183" />][7] From the diagram above, we can see that the most fundamental type is the `PIXI.DisplayObject` class, which every other display object inherits from. This class represents the absolute essential elements that are required for an object to be rendered to the screen. 
> When I say *display object* I am not just referring to the `PIXI.DisplayObject` class. Rather I'm referring to `PIXI.DisplayObject` and *all* the other objects that inherit from it. Essentially when I use the term *display object* I'm referring to any object that can be rendered to the screen by pixi.js. Next in the chain is `PIXI.DisplayObjectContainer`, which allows an object to act as a container for other display objects. The `addChild()` method, which we used in the first tutorial, is an example of a method that is provided by `PIXI.DisplayObjectContainer` and is also available by means of inheritance within `PIXI.Stage`,` PIXI.Sprite`, and `PIXI.TilingSpite`. Essentially each class within the inheritance tree is a more specialized version of the class it inherits from. The good news is that we can take advantage of inheritance to create our own specialized display objects. In other words, we can write specialized classes that represent each of our parallax layers and have pixi.js handle them as if they were just another display object. This gives us a really great way of encapsulating our code and keeping everything nice and tidy. 
### Making the Far Layer a Specialized Display Object So let's start with the far layer. Open your index.html file and look for the lines of code within the init() function that create and setup the layer. Here's what you're looking for: 

    var farTexture = PIXI.Texture.fromImage("resources/bg-far.png");
    far = new PIXI.TilingSprite(farTexture, 512, 256);
    far.position.x = 0;
    far.position.y = 0;
    far.tilePosition.x = 0;
    far.tilePosition.y = 0;
    stage.addChild(far); It would be ideal if we could create a class that represents the far layer and hide much of our implementation details within the class. So instead of the code we have above, we want to get to the point where the following will do instead: 

    far = new Far();
    stage.addChild(far); A huge reduction in code right? Plus I'd like to think it's significantly more readable than our original attempt. Let's work towards that by creating a class named 

`Far` that represent our scroller's far layer. Create a new file in your project's root folder and name it `Far.js`. Now define a function named `Far`, which will represent our class' constructor: 
    function Far(texture, width, height) {
      PIXI.TilingSprite.call(this, texture, width, height);
    } Below the constructor add the following two lines and then save your file: 

    function Far(texture, width, height) {
      PIXI.TilingSprite.call(this, texture, width, height);
    }
    
    <strong>Far.constructor = Far;
    Far.prototype = Object.create(PIXI.TilingSprite.prototype);</strong> These two lines set the constructor to be our Far function and also inherit the features of the 

`PIXI.TilingSprite` class. 
> A constructor is a special type of function that creates a class instance. In JavaScript, the constructor's name is also used to specify the name of your class. So why does our `Far` class inherit from `PIXI.TilingSprite`? Well, if you remember from the first tutorial, we used instances of `TilingSprite` to represent each of our parallax layers. It therefore makes sense to utilise these features within our own specialized class. Essentially what we're saying is this: Our `Far` class is a more specialized version of `PIXI.TilingSprite`. Because our `Far` class inherits from `PIXI.TilingSprite`, we need to remember to initialize the `TilingSprite` functionality. That's done by calling the `TilingSprite` class' constructor from within our own class' constructor. I've highlighted the line of code that does that below: 
    function Far(texture, width, height) {
      <strong>PIXI.TilingSprite.call(this, texture, width, height);</strong>
    }
    
    Far.constructor = Far;
    Far.prototype = Object.create(PIXI.TilingSprite.prototype); This is done because we want our 

`Far` class to inherit all of the functionality of `TilingSprite`. Since `TilingSprite` requires three parameters to be passed to its constructor, we need to make sure our own class takes these arguments and uses them to initialize the tiling sprite. Here's another look at your class with the parameters highlighted: 
    function Far(<strong>texture, width, height</strong>) {
      PIXI.TilingSprite.call(this, <strong>texture, width, height</strong>);
    }
    
    Far.constructor = Far;
    Far.prototype = Object.create(PIXI.TilingSprite.prototype); We've still got some additional functionality to add to our 

`Far` class, but we are actually already in a position to start integrating it into our `index.html` page. 
### Instantiating your Far class Move back to your 

`index.html` page. In order to use your `Far` class you'll need to include its source file. Add the following line near the top of the page's body: 
    <body onload="init();">
      <div align="center">
        <canvas id="game-canvas" width="512" height="384"></canvas>
      </div>
      <script src="pixi.js-master/bin/pixi.dev.js"></script>
      <strong><script src="Far.js"></script></strong> Now scroll down a little and remove the following line: 

      var farTexture = PIXI.Texture.fromImage("resources/bg-far.png");
      <span style="color: #990000;text-decoration: line-through">far = new PIXI.TilingSprite(farTexture, 512, 256);</span>
      far.position.x = 0;
      far.position.y = 0;
      far.tilePosition.x = 0;
      far.tilePosition.y = 0;
      stage.addChild(far); Replace it with this: 

      var farTexture = PIXI.Texture.fromImage("resources/bg-far.png");
      <strong>far = new Far(farTexture, 512, 256);</strong>
      far.position.x = 0;
      far.position.y = 0;
      far.tilePosition.x = 0;
      far.tilePosition.y = 0;
      stage.addChild(far); Okay, I'll admit that it's not much of an improvement at the moment, but we can now actually start to hide much more of the code directly within our Far class. Let's go ahead and do just that. 

### Encapsulating Positioning Within 

`index.html` we currently set the `position` and `tilePosition` properties of our far layer. Let's remove that functionality and instead encapsulate it within our `Far` class. Start by removing the original code from `index.html`: 
      var farTexture = PIXI.Texture.fromImage("resources/bg-far.png");
      far = new Far(farTexture, 512, 256);
      <span style="color: #990000;text-decoration: line-through">far.position.x = 0;</span>
      <span style="color: #990000;text-decoration: line-through">far.position.y = 0;</span>
      <span style="color: #990000;text-decoration: line-through">far.tilePosition.x = 0;</span>
      <span style="color: #990000;text-decoration: line-through">far.tilePosition.y = 0;</span>
      stage.addChild(far); Save your changes and move to your 

`Far.js` file. Now set the layer's `position` and `tilePosition` properties directly within the class' own constructor: 
    function Far(texture, width, height) {
      PIXI.TilingSprite.call(this, texture, width, height);
    
      <strong>this.position.x = 0;
      this.position.y = 0;
      this.tilePosition.x = 0;
      this.tilePosition.y = 0;</strong>
    } If you're unfamiliar with object-oriented JavaScript or object-oriented programming in general then you may be wondering what the purpose of the 

`this` keyword is in our code above. Basically this lets you refer to a created instance of your class. And through `this` we can reference all the properties and methods of that instance. Because our `Far` class inherits from `PIXI.TilingSprite` it also has all the properties and methods of `TilingSprite` including `position` and `tilePosition`. To access those properties we simply use the `this` keyword. Here's the line of code that sets our layer's x-position again: 
    <strong>this</strong>.position.x = 0; It should also be noted that the 

`this` keyword is also used to reference any new properties or methods that you may add to your class. Now save your changes and test your code within the browser. Everything should run as expected. Also check that no errors are being thrown by taking a look in Chrome's JavaScript Console. 
> Simply press **F12** (**Cmd + Opt + i** on Mac) to open the Developer Tools window then click the** Console** tab.
### Encapsulating the Layer's Texture Okay, we're starting to get somewhere. If you look back at your 

`index.html` page you should see that things are starting to look a bit more succinct: 
    <strong>var farTexture = PIXI.Texture.fromImage("resources/bg-far.png");
    far = new Far(farTexture, 512, 256);
    stage.addChild(far);</strong> But there's still room for improvement. After all, if we can hide our positioning code directly within the far layer's class, then why can't we do the same with its texture? Well we can. So let's give that a go too. Move to your Far.js file and add a line at the start of your constructor to create the layer's texture: 

    function Far(texture, width, height) {
      <strong>var texture = PIXI.Texture.fromImage("resources/bg-far.png");</strong>
      PIXI.TilingSprite.call(this, texture, width, height); Now explicitly pass the width and height of your texture into the 

`TilingSprite`'s constructor: 
    function Far(texture, width, height) {
      var texture = PIXI.Texture.fromImage("resources/bg-far.png");
      PIXI.TilingSprite.call(this, texture, <strong>512, 256</strong>); Since we're now handling the texture directly within the class there's really no need to pass the 

`texture`, `width`, and `height` parameters to our constructor. Remove all three parameters and save your code: 
    function Far(<span style="color: #990000;text-decoration: line-through">texture, width, height</span>) { Your constructor function should now look like this: 

    <strong>function Far() {
      var texture = PIXI.Texture.fromImage("resources/bg-far.png");
      PIXI.TilingSprite.call(this, texture, 512, 256);
    
      this.position.x = 0;
      this.position.y = 0;
      this.tilePosition.x = 0;
      this.tilePosition.y = 0;
    }</strong> All that's left to do now is to pop back over to your 

`index.html` file and remove the texture that we'd previously been creating and passing to the far layer's constructor: 
    <span style="color: #990000;text-decoration: line-through">var farTexture = PIXI.Texture.fromImage("resources/bg-far.png");</span>
    far = new Far(<span style="color: #990000;text-decoration: line-through">farTexture, 512, 256</span>);
    stage.addChild(far); Your code should now look like this: 

    <strong>far = new Far();
    stage.addChild(far);</strong> Much simpler and clearer than before, right? All our layer's ugly implementation details are now safely hidden away within our 

`Far` class. Make sure both `index.html` and `Far.js` are saved then test the latest version of your code in Chrome. 
### Doing the Same for the Mid Layer I've taken some time to walk you through the steps required to create a class that represents our parallax scroller's far layer. The class inherits from 

`PIXI.TilingSprite` and behaves like any other pixi.js display object. While we haven't yet finished adding to it, we'll briefly stop and apply what we've learned to create a class that represents our parallax scroller's mid layer. Create a new file named `Mid.js` and to begin with add the following code to it: 
    <strong>function Mid() {
    }
    
    Mid.constructor = Mid;
    Mid.prototype = Object.create(PIXI.TilingSprite.prototype);</strong> Now within the constructor, create the mid layer's texture and also set its positioning properties: 

    function Mid() {
      <strong>var texture = PIXI.Texture.fromImage("resources/bg-mid.png");
      PIXI.TilingSprite.call(this, texture, 512, 256);
    
      this.position.x = 0;
      this.position.y = 128;
      this.tilePosition.x = 0;
      this.tilePosition.y = 0;</strong>
    }
    
    Mid.constructor = Mid;
    Mid.prototype = Object.create(PIXI.TilingSprite.prototype); Save your 

`Mid.js` file then move to index.html and include your Mid class' source file: 
    <body onload="init();">
      <div align="center">
        <canvas id="game-canvas" width="512" height="384"></canvas>
      </div>
      <script src="pixi.js-master/bin/pixi.dev.js"></script>
      <script src="Far.js"></script>
      <strong><script src="Mid.js"></script></strong> With that done, scroll down to your 

`init()` function and remove the following lines: 
    far = new Far();
    stage.addChild(far);
    
    <span style="color: #990000;text-decoration: line-through">var midTexture = PIXI.Texture.fromImage("resources/bg-mid.png");
    mid = new PIXI.TilingSprite(midTexture, 512, 256);
    mid.position.x = 0;
    mid.position.y = 128;
    mid.tilePosition.x = 0;
    mid.tilePosition.y = 0;</span>
    stage.addChild(mid); Replace them with this single line of code: 

    far = new Far();
    stage.addChild(far);
    
    <strong>mid = new Mid();</strong>
    stage.addChild(mid); Save your 

`Mid.js` file and test your latest version in a browser. As usual, check for any JavaScript errors at runtime and make sure that your scroller still performs as expected. 
### Writing an update() Method We've already done a significant amount of refactoring to our codebase, but there are still a few things we can do. Move back to your 

`index.html` file and scroll down to its main update loop. It should look like this: 
    <strong>function update() {
      far.tilePosition.x -= 0.128;
      mid.tilePosition.x -= 0.64;
    
      renderer.render(stage);
      requestAnimFrame(update);
    }</strong> The first two lines within the method scroll our layers by updating their 

`tilePosition` properties. However, there's currently a slight issue with our code: by directly changing the `tilePosition` properties we are exposing the internals of our `Mid` and `Far` classes. This goes against the object-oriented principle of encapsulation. Ideally we want to hide the implementation within our class. Our code would read much better if both classes simply had an `update()` method that actually performed the scrolling for us. In other words, something like this would be more desirable for our main loop: 
    function update() {
      <strong>far.update();
      mid.update();</strong>
    
      renderer.render(stage);
    
      requestAnimFrame(update);
    } Thankfully such a change is trivial. We'll add an < method to both our 

`Far` and `Mid` classes that scrolls each layer by a small amount. Starting with the far layer, open `Far.js` and add the following method to it: 
    Far.constructor = Far;
    Far.prototype = Object.create(PIXI.TilingSprite.prototype);
    
    <strong>Far.prototype.update = function() {
      this.tilePosition.x -= 0.128;
    };</strong> The method's body should look familiar to you. It simply shifts your texture's tile position by 0.128 pixels, which is exactly what we currently do within your main loop in 

`index.html`. Okay, save your changes and add a similar method to `Mid.js`: 
    Mid.constructor = Mid;
    Mid.prototype = Object.create(PIXI.TilingSprite.prototype);
    
    <strong>Mid.prototype.update = function() {
      this.tilePosition.x -= 0.64;
    };</strong> The only difference between the two methods is that the mid layer's 

`update()` method scrolls it by a greater amount. Save your changes and move back to `index.html`. Now all we need to do is call each layer's `update()` method from within our main loop. Remove the following two lines of code: 
    function update() {
      <span style="color: #990000;text-decoration: line-through">far.tilePosition.x -= 0.128;</span>
      <span style="color: #990000;text-decoration: line-through">mid.tilePosition.x -= 0.64;</span>
    
      renderer.render(stage);
    
      requestAnimFrame(update);
    } And replace them with: 

    function update() {
      <strong>far.update();
      mid.update();</strong>
    
      renderer.render(stage);
    
      requestAnimFrame(update);
    } Save your changes and test that everything continues to run as expected in Chrome. 

### A Pause for Thought While your parallax scroller still behaves the same, we've actually made some significant changes to the overall architecture of the code. We've adopted a more object-oriented design by taking advantage of inheritance to create two specialized display objects that represent our parallax layers. Being able to write specialized display objects is a powerful concept that comes in handy in many situations. Both our 

`Far` and `Mid` classes are essentially treated just like any other display object supported by pixi.js. The following diagram illustrates where our two specialized classes sit within the inheritance structure of Pixi's display object classes. [<img class="alignnone size-full wp-image-1271" alt="ps-tut2-screenshot2" src="http://flippinawesome.org/wp-content/uploads/2013/11/ps-tut2-screenshot2.png" width="432" height="183" />][8] Before proceeding it's worth taking a look over your code files and ensuring that everything we've done so far makes sense. There isn't really a lot of code but if you're new to object-oriented programming then it may take a little time to fully digest. 
## Creating a Scroller Class One of the goals outlined at the beginning of this tutorial was to wrap our parallax scroller into its own self contained class. Now that we've written our 

`Far` and `Mid` classes, we're now in a position to do that. By doing so we'll be able to remove our `Mid` and `Far` instances from `index.html` and instead encapsulate them within a single object that takes care of all our scrolling needs. Let's write a class that will do that. Create a new JavaScript file named `Scroller.js` and define a class named `Scroller` by adding the following code to it: 
    <strong>function Scroller(stage) {
    }</strong> There are two things worth noting about this class. Firstly, its constructor function expects a reference to our stage (

`Pixi.Stage`). Secondly, it doesn't inherit from anything. Unlike the `Far` and `Mid` classes, our `Scroller` class will not be a specialized display object. Instead it'll add our far and mid layers using the constructor's stage parameter. Let's start by setting up our far layer within the class: 
    function Scroller(stage) {
      <strong>this.far = new Far();
      stage.addChild(this.far);</strong>
    } The first line of code creates an instance of the 

`Far` class. Notice that we store our instance within a member variable named `far`. 
> Member variables are created by adding properties directly to your class via the `this` keyword. A member variable has the advantage of persisting throughout the lifetime of your class instance, meaning that any other methods of your class can access it too. The second line adds the far layer to the stage. Now let's do the same for the mid layer. Add the following additional two lines to your constructor: 
    function Scroller(stage) {
      this.far = new Far();
      stage.addChild(this.far);
    
      <strong>this.mid = new Mid();
      stage.addChild(this.mid);</strong>
    } Our class now has two member variables: 

`far` and `mid`. This is useful as it will allow us to access our parallax layers from within any other methods that we may add to the class. This is quite convenient as we do indeed need to add an additional method. It'll be used to update the position of both layers. Let's go ahead and add this method now: 
    function Scroller(stage) {
      this.far = new Far();
      stage.addChild(this.far);
    
      this.mid = new Mid();
      stage.addChild(this.mid);
    }
    
    <strong>Scroller.prototype.update = function() {
      this.far.update();
      this.mid.update();
    };</strong> Remember we wrote 

`update()` methods for both our `Mid` and `Far` classes? Well all that's required within our `Scroller` class' own `update()` method is to call these `update` methods. 
### Plugging in the Scroller Class So now that we have a 

`Scroller` class that represents our parallax scroller, we can move back to our `index.html` page and plug it in. Open `index.html` and include `Scroller.js`: 
    <script src="pixi.js-master/bin/pixi.dev.js"></script>
    <script src="Far.js"></script>
    <script src="Mid.js"></script>
    <strong><script src="Scroller.js"></script></strong> Now move down to your 

`init()` function and remove the following lines of code: 
    function init() {
      stage = new PIXI.Stage(0x66FF99);
      renderer = new PIXI.autoDetectRenderer(
        512,
        384,
        document.getElementById("game-canvas")
      );
    
      <span style="color: #990000;text-decoration: line-through">far = new Far();</span>
      <span style="color: #990000;text-decoration: line-through">stage.addChild(far);</span>
    
      <span style="color: #990000;text-decoration: line-through">mid = new Mid();</span>
      <span style="color: #990000;text-decoration: line-through">stage.addChild(mid);</span>
    
      requestAnimFrame(update);
    } Remember, both the far and mid layers will now be taken care of by your 

`Scroller` class. So let's create a `Scroller` instance to replace the lines we've just removed: 
    function init() {
      stage = new PIXI.Stage(0x66FF99);
      renderer = new PIXI.autoDetectRenderer(
        512,
        384,
        document.getElementById("game-canvas")
      );
    
      <strong>scroller = new Scroller(stage);</strong>
    
      requestAnimFrame(update);
    } Notice also that we passed a stage reference into the 

`Scroller` class' constructor. It's important we do this as the `Scroller` class requires the reference in order to add its far and mid parallax layers to the display list. Now all we need to do is call the scroller's `update()` method within our main loop. First, remove the following two lines from our main loop: 
    function update() {
      <span style="color: #990000;text-decoration: line-through">far.update();</span>
      <span style="color: #990000;text-decoration: line-through">mid.update();</span>
    
      renderer.render(stage);
    
      requestAnimFrame(update);
    } Now add the following line to update the scroller: 

    function update() {
      <strong>scroller.update();</strong>
    
      renderer.render(stage);
    
      requestAnimFrame(update);
    } Save your changes and test everything with Chrome. As always look out for any runtime errors in the JavaScript Console and double-check your code if there are any. We've successfully re-architected our parallax scroller so that everything is contained within a single class. If you look over 

`index.html` you'll see that we've hidden away all the implementation code that we wrote last time in the first tutorial. 
## Adding a Viewport We've made huge strides but there's one more thing we'll add. For our scroller to be complete we'll need to add the concept of a viewport. Think of the viewport as a window looking onto your game map. Don't we already have a viewport you may be asking? After all, when you run your code in a browser, we only see what's viewable within our stage's bounds. That's entirely true but there's currently no way to know how far we've scrolled within the game world. Also, wouldn't it be nice if we could simply jump to a certain position and see exactly how our layers should look? Well all that will be possible once we add the concept of a viewport and provide a means of setting its current position. 

### Adding a setViewportX() Method to the Scroller Class We currently have an 

`update()` method that we use to continuously scroll our parallax layers. Let's replace that with a new method named `setViewportX()` which we can use to set the horizontal position of the viewport. Calling this method will let us arbitrarily position our game map. Let's start with the `Scroller` class. Open `Scroller.js` and remove the existing `update()` method: 
    function Scroller(stage) {
      this.far = new Far();
      stage.addChild(this.far);
    
      this.mid = new Mid();
      stage.addChild(this.mid);
    }
    
    <span style="color: #990000;text-decoration: line-through">Scroller.prototype.update = function() {</span>
      <span style="color: #990000;text-decoration: line-through">this.far.update();</span>
      <span style="color: #990000;text-decoration: line-through">this.mid.update();</span>
    <span style="color: #990000;text-decoration: line-through">};</span> Now replace it with a 

`setViewportX()` method and save your changes: 
    function Scroller(stage) {
      this.far = new Far();
      stage.addChild(this.far);
    
      this.mid = new Mid();
      stage.addChild(this.mid);
    }
    
    <strong>Scroller.prototype.setViewportX = function(viewportX) {
      this.far.setViewportX(viewportX);
      this.mid.setViewportX(viewportX);
    };</strong> Our 

`setViewportX()` method is fairly straightforward. It expects a number to be passed as the method's `viewportX` parameter and then passes that value to each of our layers. As you can see, both our layers need to implement their own `setViewportX()` methods. Let's go ahead and do that now. 
### Adding a setViewportX() Method to the Far Class We'll start by removing the class' existing 

`update()` method. Open `Far.js` and remove the following lines: 
    function Far() {
      var texture = PIXI.Texture.fromImage("resources/bg-far.png");
      PIXI.TilingSprite.call(this, texture, 512, 256);
    
      this.position.x = 0;
      this.position.y = 0;
      this.tilePosition.x = 0;
      this.tilePosition.y = 0;
    }
    
    Far.constructor = Far;
    Far.prototype = Object.create(PIXI.TilingSprite.prototype);
    
    <span style="color: #990000;text-decoration: line-through">Far.prototype.update = function() {</span>
      <span style="color: #990000;text-decoration: line-through">this.tilePosition.x -= 0.128;</span>
    <span style="color: #990000;text-decoration: line-through">};</span> We'll need to be able to track the viewport's horizontal position. Define a member variable within the class' constructor for this purpose: 

    function Far() {
      var texture = PIXI.Texture.fromImage("resources/bg-far.png");
      PIXI.TilingSprite.call(this, texture, 512, 256);
    
      this.position.x = 0;
      this.position.y = 0;
      this.tilePosition.x = 0;
      this.tilePosition.y = 0;
    
      <strong>this.viewportX = 0;</strong>
    } Now let's add the following constant to our class: 

    Far.constructor = Far;
    Far.prototype = Object.create(PIXI.TilingSprite.prototype);
    
    <strong>Far.DELTA_X = 0.128;</strong> The value of our 

`DELTA_X` constant should look familiar to you. It's the number of pixels that we previously shifted the layer's tile position by on each `update()` call. Of course using constants makes our code more readable and maintainable, which is why we've opted to use one. Basically we'll use the constant to move the far layer by 0.128 pixels every time our viewport moves a single unit. So let's now write the `setViewportX()` method that will do that for us. Add the following: 
    Far.constructor = Far;
    Far.prototype = Object.create(PIXI.TilingSprite.prototype);
    
    Far.DELTA_X = 0.128;
    
    <strong>Far.prototype.setViewportX = function(newViewportX) {
      var distanceTravelled = newViewportX - this.viewportX;
      this.viewportX = newViewportX;
      this.tilePosition.x -= (distanceTravelled * Far.DELTA_X);
    };</strong> The code above isn't too difficult to follow. First we calculate the distance travelled since 

`setViewportX()` was last called. The viewport's new horizontal position is then stored within our `viewportX` member variable. Lastly, we multiply the distance travelled by our `DELTA_X` constant to determine how far to shift the layer's tile position by. 
> It should be noted that our x-position represents the left hand side of the viewport's window. It's also quite common in other implementations for the x-position to represent the viewport's center. Save the latest version of `Far.js`. Now we need to make the same changes to our `Mid` class. 
### Adding a setViewportX() Method to the Mid Class The 

`Mid` class' code is almost identical to our `Far` class, so we'll be quick. Open Mid.js and remove its `update()` method: 
    function Mid() {
      var texture = PIXI.Texture.fromImage("resources/bg-mid.png");
      PIXI.TilingSprite.call(this, texture, 512, 256);
    
      this.position.x = 0;
      this.position.y = 128;
      this.tilePosition.x = 0;
      this.tilePosition.y = 0;
    }
    
    Mid.constructor = Mid;
    Mid.prototype = Object.create(PIXI.TilingSprite.prototype);
    
    <span style="color: #990000;text-decoration: line-through">Mid.prototype.update = function() {</span>
      <span style="color: #990000;text-decoration: line-through">this.tilePosition.x -= 0.64;</span>
    <span style="color: #990000;text-decoration: line-through">};</span> Now add the following lines to your class: 

    function Mid() {
      var texture = PIXI.Texture.fromImage("resources/bg-mid.png");
      PIXI.TilingSprite.call(this, texture, 512, 256);
    
      this.position.x = 0;
      this.position.y = 128;
      this.tilePosition.x = 0;
      this.tilePosition.y = 0;
    
      <strong>this.viewportX = 0;</strong>
    }
    
    Mid.constructor = Mid;
    Mid.prototype = Object.create(PIXI.TilingSprite.prototype);
    
    <strong>Mid.DELTA_X = 0.64;
    
    Mid.prototype.setViewportX = function(newViewportX) {
      var distanceTravelled = newViewportX - this.viewportX;
      this.viewportX = newViewportX;
      this.tilePosition.x -= (distanceTravelled * Mid.DELTA_X);
    };</strong> The only difference between the two classes is that the 

`Mid` class' `DELTA_X` constant has a value of 0.64, which is to ensure that its layer scrolls faster than the far layer. Save your changes. 
### Testing the Viewport We should test the viewport and make sure that setting its position is reflected in our parallax layers. First we'll need to open 

`index.html` and remove its old call to the scroller's `update()` method. Simply remove the following line from your main loop: 
    function update() {
      <span style="color: #990000;text-decoration: line-through">scroller.update();</span>
    
      renderer.render(stage);
    
      requestAnimFrame(update);
    } Save the 

`index.html` file and test your changes within a browser. You should notice that you see your parallax layers but neither is scrolling. That's because we haven't added any code to actually change the viewport's horizontal position. Currently it's fixed at it's default x-position of 0. Before we do add the code to do that, let's test that our scroller's `setViewportX()` actually works. We can do that with Chrome's JavaScript console. 
> Simple press **F12** (**Cmd + Opt + i** on Mac) to open the Developer Tools window then click the**Console** tab. Open Chrome's JavaScript console and try moving the viewport to the right by 50 pixels by entering the following: 
    scroller.setViewportX(50);

> The JavaScript console has access to any global variables within your program. We can therefore access the scroller via our global `scroller` variable and call its `setViewportX()` method. You should see your parallax layers move to the left, indicating that we have successfully repositioned the viewport. Try moving the viewport to an x-position of 7000: 
    scroller.setViewportX(7000); Again, our parallax layers should update to reflect our new position within our game world. Try a few more viewport values to satisfy yourself that everything is working as expected. 

### Scrolling the Viewport It should be obvious that we can simulate movement through our game world by continuously updating our scroller's viewport position. We can do this from within our main loop but to do that we'll need to be able to obtain the viewport's current horizontal position. Let's go ahead and add a new method to our 

`Scroller` class to allow us to do that. 
### Obtaining the Viewport's Position Currently our 

`Scroller` class does not store the current viewport position, so we'll need a member variable for that purpose. Open `Scroller.js` and define the following member variable within the constructor: 
    function Scroller(stage) {
      this.far = new Far();
      stage.addChild(this.far);
    
      this.mid = new Mid();
      stage.addChild(this.mid);
    
      <strong>this.viewportX = 0;</strong>
    } And update the value of the 

`viewportX` member variable within your `setViewportX()` method: 
    Scroller.prototype.setViewportX = function(viewportX) {
      <strong>this.viewportX = viewportX;</strong>
      this.far.setViewportX(viewportX);
      this.mid.setViewportX(viewportX);
    }; With that done we can write a getViewportX() method that will return the viewport's current position. Add the following: 

    Scroller.prototype.setViewportX = function(viewportX) {
      this.viewportX = viewportX;
      this.far.setViewportX(viewportX);
      this.mid.setViewportX(viewportX);
    };
    
    <strong>Scroller.prototype.getViewportX = function() {
      return this.viewportX;
    };</strong> Save your work. 

### Updating your Main Loop Now all that's left is to actually continuously update your scroller's viewport position. We'll do this inside your project's main loop. Open 

`index.html` and simply add the following two lines of code: 
    function update() {
      <strong>var newViewportX = scroller.getViewportX() + 5;
      scroller.setViewportX(newViewportX);</strong>
    
      renderer.render(stage);
    
      requestAnimFrame(update);
    } The first line obtains the viewport's x-position and increments it by 5 units. The second line takes the new value and sets the viewport's current x-position to it. Essentially it forces the viewport to scroll by 5 units on every call to our main loop. Save your work and try running it within Chrome. You should once again see your parallax layers scrolling away. Try experimenting with different scroll speeds. For example, try incrementing the viewport by 15 units instead of 5. 

### Moving the Viewport Let's add one more method to the 

`Scroller` class that will let you move the viewport a specified distance from its current position. This will help us make our main loop just a little bit cleaner still. Open `Scroller.js` and add the following method before saving your changes: 
    Scroller.prototype.getViewportX = function() {
      return this.viewportX;
    };
    
    <strong>Scroller.prototype.moveViewportXBy = function(units) {
      var newViewportX = this.viewportX + units;
      this.setViewportX(newViewportX);
    };</strong> Like everything we've done, this new method isn't hard to understand. It simply works out what the viewport's new position will be then makes a call to the class' 

`setViewportX()` method to actually set the viewport position. Move back to `index.html` and remove the following lines: 
    function update() {
      <span style="color: #990000;text-decoration: line-through">var newViewportX = scroller.getViewportX() + 5;</span>
      <span style="color: #990000;text-decoration: line-through">scroller.setViewportX(newViewportX);</span>
    
      renderer.render(stage);
    
      requestAnimFrame(update);
    } Replace them with this single line that uses your 

`moveViewportXBy()` method: 
    function update() {
      <strong>scroller.moveViewportXBy(5);</strong>
    
      renderer.render(stage);
    
      requestAnimFrame(update);
    } Save your changes and test your project within your web browser. 

## The Main Entry Point Revisited The second part of this tutorial is almost at an end. Before we finish though let's revisit 

`index.html` and do one final piece of re-factoring. While we've done an admirable job of reducing our dependency on global variables, our `index.html` file still has a few globals floating around in there. In fact, in large applications it's also good practice to separate as much JavaScript as possible from your HTML pages. While there isn't much JavaScript left in our HTML page we can do even better. Let's take the code that's sitting there and encapsulate it within its own class named `Main`. That way, the global variables that we currently rely upon will become member variables of our new class instead. Create a new file and name it `Main.js`. Create a constructor function for the class and place the code from your HTML page's `init()` function within it: 
    <strong>function Main() {
      this.stage = new PIXI.Stage(0x66FF99);
      this.renderer = new PIXI.autoDetectRenderer(
        512,
        384,
        document.getElementById("game-canvas")
      );
    
      this.scroller = new Scroller(this.stage);
    
      requestAnimFrame(this.update.bind(this));
    }</strong> Notice the use of the 

`this` keyword above. We use it to define `stage`, `renderer` and `scroller` as member variables. The `this` keyword is also used in our call to the JavaScript function `requestAnimFrame()`. Here's the line of code again: 
    requestAnimFrame(this.update.bind(this)); It's used here to specify that a method of our class named 

`update()` (we still need to write this method) will be called when the stage can next be redrawn. Additionally another JavaScript function that you may not be familiar with, named `bind()`, is also called. This is used to guarantee that when `update()` is called that it's correctly scoped to our `Main` class' instance. If you don't apply `bind()` then your `update()` method won't be able to access and use any of our class' member variables. Okay, let's actually write our class' `update()` method. It will simply contain the code from our HTML page's `update()` function: 
    <strong>Main.prototype.update = function() {
      this.scroller.moveViewportXBy(Main.SCROLL_SPEED);
      this.renderer.render(this.stage);
      requestAnimFrame(this.update.bind(this));
    };</strong> Once again we've used the 

`this` keyword where required and also taken advantage of JavaScript's `bind()` function to ensure that our update loop is always correctly scoped. Also, notice that our code above used a constant named `SCROLL_SPEED` when calling the scroller's `moveViewportXBy()` method. Previously we'd just been passing a hard-coded value. Let's actually add that constant to our `Main` class. Add the following line directly after the constructor: 
      requestAnimFrame(this.update.bind(this));
    }
    
    <strong>Main.SCROLL_SPEED = 5;</strong>
    
    Main.prototype.update = function() { Okay, save your work. Now let's open 

`index.html` and remove the old code that we've just finished moving inside our `Main` class. Remove the following lines: 
    <script>
      function init() {
        <span style="color: #990000;text-decoration: line-through">stage = new PIXI.Stage(0x66FF99);</span>
        <span style="color: #990000;text-decoration: line-through">renderer = new PIXI.autoDetectRenderer(</span>
          <span style="color: #990000;text-decoration: line-through">512,</span>
          <span style="color: #990000;text-decoration: line-through">384,</span>
          <span style="color: #990000;text-decoration: line-through">document.getElementById("game-canvas")</span>
        <span style="color: #990000;text-decoration: line-through">);</span>
    
        <span style="color: #990000;text-decoration: line-through">scroller = new Scroller(stage);</span>
    
        <span style="color: #990000;text-decoration: line-through">requestAnimFrame(update);</span>
      <span style="color: #990000;text-decoration: line-through">}</span>
    
      <span style="color: #990000;text-decoration: line-through">function update() {</span>
        <span style="color: #990000;text-decoration: line-through">scroller.moveViewportXBy(5);</span>
    
        <span style="color: #990000;text-decoration: line-through">renderer.render(stage);</span>
    
        <span style="color: #990000;text-decoration: line-through">requestAnimFrame(update);</span>
      }
    </script> Replace your code with a new 

`init()` function that simply instantiates the `Main` class: 
    <script>
      function init() {
        <strong>main = new Main();</strong>
      }
    </script> Finally, include your class' 

`Main.js` file by adding the following line: 
    <script src="pixi.js-master/bin/pixi.dev.js"></script>
    <script src="Far.js"></script>
    <script src="Mid.js"></script>
    <script src="Scroller.js"></script>
    <strong><script src="Main.js"></script></strong> Save your work and test that everything still runs within Google Chrome. We've successfully moved everything into a main application class with only a few lines of JavaScript remaining in 

`index.html` to kick everything off. 
## Conclusion Phew! We've covered a significant amount of ground. While the end result is the same (we still just have two scrolling parallax layers), I hope you'll see the benefit of re-architecting our scroller's codebase. Everything is now cleaner and we have a single 

`Scroller` class that's used to manage our parallax layers. And while our focus this time wasn't so much on pixi.js, you should at least now appreciate the advantages of extending Pixi's display object classes. 
## Next Time All these changes have put is in an ideal position to start work on a third, more complex, parallax layer. This layer will act as your game world's map and will be built from a series of sprites as opposed to being a simple repeating texture. We'll also set our sights back on pixi.js by covering all sorts of goodies including sprite sheets, texture frames, and object pooling. And remember, the source code for both this and previous tutorial in the series is 

[available on GitHub][9]. See you next time. *This article was originally published at <http://www.yeahbutisitflash.com/?p=5666>*

 [1]: /authors/christopher-caleb
 [2]: http://www.adamatomic.com/canabalt/
 [3]: https://chrome.google.com/webstore/detail/monster-dash/cknghehebaconkajgiobncfleofebcog?hl=en
 [4]: http://www.pixijs.com/
 [5]: http://www.yeahbutisitflash.com/pixi-parallax-scroller/tutorial-1/index.html
 [6]: http://flippinawesome.org/2013/11/04/building-a-parallax-scrolling-game-with-pixi-js/
 [7]: http://flippinawesome.org/wp-content/uploads/2013/11/ps-tut2-screenshot1.png
 [8]: http://flippinawesome.org/wp-content/uploads/2013/11/ps-tut2-screenshot2.png
 [9]: https://github.com/ccaleb/pixi-parallax-scroller