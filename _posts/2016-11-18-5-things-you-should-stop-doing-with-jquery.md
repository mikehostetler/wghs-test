---
ID: 61
post_title: >
  5 Things You Should Stop Doing With
  jQuery
author: admin
post_date: 2016-11-18 17:07:53
post_excerpt: ""
layout: post
permalink: >
  http://modern-web.dev/2016/11/18/5-things-you-should-stop-doing-with-jquery/
published: true
featured_author:
  - 'No'
  - 'No'
---
<em>By <a href="/authors/burke-holland">Burke Holland</a></em>

When I first started using jQuery, I was so excited. I was using vanilla JS and really struggling with understanding when elements on a page were ready and how I could access them. When I learned about jQuery, I think I did what many people do. With tears of joy running down my face, I opened up a document ready function and then just vomited up massive amounts of jQuery.

Some of the worst jQuery code ever written was written by me - I can assure you of that.

<a href="http://flippinawesome.org/wp-content/uploads/2013/05/tumblr_inline_mm6t2vw1RX1qz4rgp.jpg"><img class="size-full wp-image-285 alignnone" alt="tumblr_inline_mm6t2vw1RX1qz4rgp" src="http://flippinawesome.org/wp-content/uploads/2013/05/tumblr_inline_mm6t2vw1RX1qz4rgp.jpg" width="471" height="300" /></a>

Since then, I've spent a few years at the Bayside of jQuery. I've had my heart broken a few times and learned a some things along the way. In doing so, I'd like to share 5 things in jQuery that I think you should think twice about doing.
<h2>1. Stop Using Document Ready</h2>
Back in the day of heavy server frameworks, knowing that your page was fully constructed before you tried to mutate it was a big deal. This was especially true if you were using partial views of some sort that were injected into the layout/master page by the server at runtime.

Nowadays, it's considered best practice to include your scripts at the bottom of the page. The HTML5 async attribute notwithstanding, scripts are loaded and executed synchronously by the browser. That means that if you have a large script in the head of your page, it will delay loading of the DOM and make your page seem slower than it actually is. Loading all scripts last will at least make your application seem to load faster. It also gives you a chance to use a spinner or loading overlay if you are completely dependent on JavaScript for your UI.

If you are adhering to the "scripts at the bottom" best practice, then you have no need for jQuery's document ready function as the HTML is already loaded by the time the script is run.
<pre><code>&lt;p id="zack"&gt;This element is on the page &lt;strong&gt;BEFORE&lt;/strong&gt; all the scripts. No document ready needed.&lt;/p&gt;

  &lt;script src="https://ajax.googleapis.com/ajax/libs/jquery/1.9.1/jquery.min.js"&gt;&lt;/script&gt;

  &lt;script type="text/javascript" charset="utf-8"&gt;

    // if you include your scripts at the very bottom, you don't need document ready
    (function($) {

      $("#zack").css("color", "green");
      $("#slator").css("color", "red");

    }(jQuery));

  &lt;/script&gt;

&lt;p id="slater"&gt;This element comes after the scripts and won't be available.&lt;/p&gt;</code></pre>
<h2>2. Stop Using The Wrong Iterator</h2>
I've got no hard evidence to back this up, but I'd be willing to guess that the each function is the most used of the jQuery utility methods. I base this on how often I am tempted to use it and how often I see it in other people's code. It's incredibly convenient for looping over DOM elements and JavaScript collections. It's also terse. There is nothing wrong with it, except that its not the only iteration function the jQuery provides. People use each like it's Zack Morris, at which point every looping problem starts looking like Kelly Kapowski.

<strong>MAP</strong>

If you have an array of items and you want to loop through it and filter out some of the items, you might be tempted to use the each method like so:
<pre><code>(function($) {

  var allStarCast = [
    { firstName: "Zack", lastName: "Morris" },
    { firstName: "Kelly", lastName: "Kapowski" },
    { firstName: "Lisa", lastName: "Turtle" },
    { firstName: "Screech", lastName: "Powers" },
    { firstName: "A.C.", lastName: "Slater" },
    { firstName: "Jessie", lastName: "Spano" },
    { firstName: "Richard", lastName: "Belding" }
  ]

  // iterate through the cast and find zack and kelly
  var worldsCutestCouple = [];
  $.each(allStarCast, function(idx, actor) {
    if (actor.firstName === "Zack" || actor.firstName === "Kelly") {
      worldsCutestCouple.push(actor);
    }
  });

  console.log(worldsCutestCouple);

}(jQuery));</code></pre>
<a href="http://jsfiddle.net/burkeholland/D8eJb/">Try It</a>

That works, but jQuery actually provides a method specifically for this scenario and it's called map. What it does is takes any items that get returned and adds them to a new array. The looping code then looks like this…
<pre><code>(function($) {

  var allStarCast = [
    { firstName: "Zack", lastName: "Morris" },
    { firstName: "Kelly", lastName: "Kapowski" },
    { firstName: "Lisa", lastName: "Turtle" },
    { firstName: "Screech", lastName: "Powers" },
    { firstName: "A.C.", lastName: "Slater" },
    { firstName: "Jessie", lastName: "Spano" },
    { firstName: "Richard", lastName: "Belding" }
  ]

  // iterate through the cast and find zack and kelly

  var worldsCutestCouple = $.map(allStarCast, function(actor, idx) {
    if (actor.firstName === "Zack" || actor.firstName === "Kelly") {
      return actor;
    }
  });

  console.log(worldsCutestCouple);

}(jQuery));</code></pre>
<a href="http://jsfiddle.net/burkeholland/4CWXF/">Try It</a>

Did you notice that the arguments for the .map() callback and the .each() callback are reversed? Watch out for that because it can bite you when you start using map.

The map function allows you to modify the items before they get slapped into the new array. I'm not currently doing that, so technically I'm still not using the "right" iterator. I should really be using grep.

<strong>GREP</strong>

If you spend most of your days on Windows, then this term might be foreign to you. The term is typically used for a Unix command line utility for searching files that contain text matching a given regular expression.

In jQuery, grep is a function whose purpose is to reduce a collection by removing elements that don't pass the test in the callback. The provided callback returns a boolean value. Return true if you want the item to remain, and false(y) if you don't. It will not affect the original array. You cannot modify the items as you reduce them.
<pre><code>(function($) {

  var allStarCast = [
      { firstName: "Zack", lastName: "Morris" },
      { firstName: "Kelly", lastName: "Kapowski" },
      { firstName: "Lisa", lastName: "Turtle" },
      { firstName: "Screech", lastName: "Powers" },
      { firstName: "A.C.", lastName: "Slater" },
      { firstName: "Jessie", lastName: "Spano" },
      { firstName: "Richard", lastName: "Belding" }
  ]

  // iterate through the cast and find zack and kelly

  var worldsCutestCouple = $.grep(allStarCast, function(actor) {
      return (actor.firstName === "Zack" || actor.firstName === "Kelly");
  });

  console.log(worldsCutestCouple);

}(jQuery));</code></pre>
<a href="http://jsfiddle.net/burkeholland/S9r3T/">Try It</a>
<h2>3. Stop Using "this"</h2>
This isn't a jQuery issue, but jQuery can definitely exacerbate the problem. The context of "this" is always changing. jQuery sometimes changes the context to something that you might not be expecting. In the each callback, this is the current item or element in the collection. In the map function, it's the window object. You can see how you could get yourself confused pretty quickly.

Have a look at the following example and then we'll talk about why it blows up.
<pre><code>(function($) {

  var sbtb = {
    log: function(message) {
      $("#log").append("").html(message);
    },
    cast: [
      { firstName: "Zack", lastName: "Morris", soExcited: false },
      { firstName: "Kelly", lastName: "Kapowski", soExcited: true },
      { firstName: "Lisa", lastName: "Turtle", soExcited: true },
      { firstName: "Screech", lastName: "Powers", soExcited: false },
      { firstName: "A.C.", lastName: "Slater", soExcited: false },
      { firstName: "Jessie", lastName: "Spano", soExcited: true },
      { firstName: "Richard", lastName: "Belding", soExcited: false }
    ],
    soExcited: function() {

    // use "this" to get a reference to the cast on this object
    $.each(this.cast, function(idx, actor) {

      // call the log function
      this.log(actor.firstName + " " + actor.lastName); // BOOM! Splosions.
      // "this" is now a cheesy actor, not the sbtb object anymore

    });

    }
  };

  sbtb.soExcited();

}(jQuery));</code></pre>
I wanted to log out everyone who was "so excited". I'm in an object so I can get a reference to the object with this. Then I loop through the object and look for the "isExcited" flag. However, as soon as I enter the loop callback, the context of this has changed and I can't access the object anymore.

Since jQuery is changing the scope for you in these loops, it's a good idea to store the reference to this somewhere so that you know it's not going to change on you.
<pre><code>(function($) {

  var sbtb = {
    log: function(message) {
      $("#log").append("<p></p>").append(message);
    },
    cast: [
      { firstName: "Zack", lastName: "Morris", isExcited: false },
      { firstName: "Kelly", lastName: "Kapowski", isExcited: true },
      { firstName: "Lisa", lastName: "Turtle", isExcited: true },
      { firstName: "Screech", lastName: "Powers", isExcited: false },
      { firstName: "A.C.", lastName: "Slater", isExcited: false },
      { firstName: "Jessie", lastName: "Spano", isExcited: true },
      { firstName: "Richard", lastName: "Belding", isExcited: false }
    ],
    soExcited: function() {

      // store this in that so we don't get confused later on when
      // our the context of "this" changes out from underneath us
      var that = this;

      // use "that" to get a reference to the cast on this object
      $.each(that.cast, function(idx, actor) {
        // call the log function
        if (actor.isExcited) {
          that.log(actor.firstName + " " + actor.lastName);
          // the value of "that" doesn't change - it's still the object
        }
      });
    }
  };

  sbtb.soExcited();

}(jQuery));</code>
</pre>

<h2>4. Stop Using ALL THE jQUERIES</h2>
jQuery has <a href="http://mathiasbynens.be/demo/jquery-size">steadily kept increasing in size</a>. This is only natural as new functionality is added. Even though the size has steadily been reduced since 1.8.3, It's led to some decry from the community claiming it "unsuitable" for mobile development due to it's sheer mass.

However, jQuery is not an all or nothing library anymore. jQuery now supports custom builds. I know it's really tempting to use the jQuery CDN and just rock on, but it is important to think about all the code that you are asking your users to download that they might not even need. That's no big deal on a desktop, but bits get precious on mobile devices, and there is no point in sending down a bunch of code to support legacy browsers if your app is a mobile one.

You have two choices here. You can head over to the <a href="https://github.com/jquery/jquery">GitHub site</a> and create a custom build with Git. It's actually really easy and I had no issues getting it to work. But, if pounding out node commands is not your thing, John Resig tweeted about a <a href="http://projects.jga.me/jquery-builder/">web UI for custom builds</a> the other day.
<h2>5. Stop Using jQuery...</h2>
...when you don't need to.

I got to a point in my development career where the first thing that I did with any project was add jQuery, even if I was just creating very simple projects and samples. I did this mainly so that I could just use the DOM selection utilities. Back in the day of older browsers, this was easier to justify, but modern browsers have this whole DOM selection thing already nailed down for you.

<strong>DOCUMENT.QUERYSELECTOR</strong>

If you are using at least IE version 8 and above, you can just map the $ to document.querySelector, which will return you the first matched element from the selector. You can pass any CSS selector to the function.

<em>Note that IE 8 only supports CSS 2.1 selectors for querySelector.</em>
<pre><code>&lt;div class="container"&gt;
  &lt;ul&gt;
    &lt;li id="pink"&gt;Pink&lt;/li&gt;
    &lt;li id="salmon"&gt;Salmon&lt;/li&gt;
    &lt;li id="blue"&gt;Blue&lt;/li&gt;
    &lt;li id="green"&gt;Green&lt;/li&gt;
    &lt;li id="red"&gt;Red&lt;/li&gt;
  &lt;/ul&gt;  
&lt;/div&gt;

&lt;script&gt;

  // create a global '$' variable
  window.$ = function(selector) {
    return document.querySelector(selector);
  };

  (function() {
    // select item1 by id and change it's background color to salmon
    var item = $("#salmon").style.backgroundColor="salmon";
    console.log(item);
  }());  
&lt;/script&gt;</code></pre>
<a href="http://jsfiddle.net/burkeholland/YPkEJ">Try It</a>

Of course, there is no chaining when using the native "style" method, so item logs out "salmon" which is what the backgroundColor method returned.

Having a DOM node is sometimes more handy than having a jQuery wrapped one. For instance, let's say we want to get a reference to an image and change it's source. Have a look at how you would do it with jQuery as opposed to a straight up DOM node.
<pre><code>// the jQuery way
$("#picture").attr("src", "http://placekitten.com/200/200");

// Vanilla JS with $ mapped to querySelector
$("#picture").src = "http://placekitten.com/200/200";</code></pre>
The DOM object gives you direct access to the "src" property of the image because you have a node of type "image" that has access that property. In the case of jQuery, everything is a jQuery object, so you have to go through the attr function to set the image source.

The document.querySelector method only gets you one element. If you call that on a collection of elements, it will return you only the first matched node. You can use document.querySelectorAll to get the entire list.

<strong>DOCUMENT.QUERYSELECTORALL</strong>

A neat trick is to map $ to querySelector (1 result) and map $$ to querySelectorAll which will give you all matched DOM elements. The tricky part of this is that querySelectorAll returns a node list which isn't terribly helpful. You are probably going to need this as an array which you can slice up. You can convert the node list to an array by using Array.prototype.slice.call(nodeList).
<pre><code>&lt;div class="container"&gt;
  &lt;ul&gt;
    &lt;li id="pink"&gt;Pink&lt;/li&gt;
    &lt;li id="salmon"&gt;Salmon&lt;/li&gt;
    &lt;li id="blue"&gt;Blue&lt;/li&gt;
    &lt;li id="green"&gt;Green&lt;/li&gt;
    &lt;li id="red"&gt;Red&lt;/li&gt;
  &lt;/ul&gt;  
&lt;/div&gt;

&lt;script&gt;

  // custom lightweight selector implementation
  // nickname: dolla

  window.$ = function(selector) {
    return document.querySelector(selector);
  };

  window.$$ = function(selector) {
    var items = {},
    results = [],
    length = 0,
    i = 0;

    // this doesn't work on IE 8- and Blackberry Browser
    results = Array.prototype.slice.call(document.querySelectorAll(selector));

    length = results.length;

    // add the results to the items object
    for ( ; i &lt; length; ) {
      items[i] = results[i];
      i++;
    }

    // add some additional properties to this items object to 
    // make it look like an array
    items.length = length;
    items.splice = [].splice();

    // add an 'each' method to the items
    items.each = function(callback) {
      var i = 0;
      for ( ; i &lt; length; ) {
        callback.call(items[i]);
        i++;
      }
    }

    return items;
  };

  // end custom selector API

  (function() {

    // select the green item and crank up the font size
    $("#green").style.fontSize = "2em";

    // select item1 by id and change it's background color to salmon
    $$("li").each(function() {
      this.style.backgroundColor = this.id;
    });
  }());

&lt;/script&gt;</code></pre>
<a href="http://jsfiddle.net/burkeholland/YnaS9/1">Try It</a>

Note that converting a nodeList to an array is not supported on IE 8 and below.

At this point you may think to yourself, "That's a lot of JavaScript to write when I can just include jQuery bro," and that's fair. This could be reduced no doubt, but jQuery puts so much convenience on objects and collections that when you start rolling without it, you see what sort of code it takes to recreate just a small piece of it - and this implementation doesn't even support IE 8 and below or Blackberry browser. Depending on what your application needs to do though, it could be far less code than including jQuery.

<a href="https://twitter.com/intelligibabble">Leland Richardson</a> and <a href="https://twitter.com/jonathansampson">Jonathan Sampson</a> helped me add a few more features to and clean up the above code. We created a repo called "<a href="https://github.com/lelandrichardson/dolla">Dolla</a>". It's not meant to be a replacement for jQuery in any way whatsoever, but rather a chance for us to learn what it's like to say "I don't need jQuery". It's a fair bit more code than you might anticipate.
<h2>Stop Reading Articles That Tell You To Stop Doing Something</h2>
Seriously though. This is just to underscore the point that these are suggestions, not absolute rules of law. There are a thousand ways that you can argue and or justify any of these items. This is merely to get you thinking about how you use jQuery and how you might be able to do it better than you do now.

So enjoy jQuery, be happy, and stay in school.