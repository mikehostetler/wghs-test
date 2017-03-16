---
ID: 243
post_title: 'Zelda: A Link to the CSS'
author: admin
post_date: 2013-04-29 12:01:07
post_excerpt: ""
layout: post
permalink: >
  http://modern-web.dev/2013/04/29/zelda-a-link-to-the-css/
published: true
dsq_thread_id:
  - "2809873124"
om_counter:
  - "31"
---
*By [Donovan Hutchinson][1]* This week, Nintendo announced a new release of the popular Zelda franchise coming soon for the Nintendo 3DS that will be set in the world of A Link to the Past. As many of you may recall, the original Zelda: A Link to the Past game that was released in the early 90's on the Super Nintendo platform. When I was growing up, that game was always my favorite, so I thought, "What better way to celebrate than to recreate part of the opening sequence to the old SNES game using nothing but HTML and CSS?" The end result will look like this: [<img class="alignnone size-full wp-image-244" alt="zelda" src="http://flippinawesome.org/wp-content/uploads/2013/04/zelda.png" width="767" height="431" />][2] If you're running a modern version of Chrome, Safari or Firefox, you can check out the [live demo][3]. 
## Getting started: HTML To get started, I created the basic HTML structure. The layout requires three 3D triangles, and some text that will sit on top of them. I began with a single triangle: 

    <div class="stage">
      <div class="triangle">
        <figure class="front"></figure>
        <figure class="back"></figure>
        <figure class="side1"></figure>
        <figure class="side2"></figure>
        <figure class="side3"></figure>
      </div>
    </div> The front and back pieces needed to be triangular, with 3 rectangles covering the left, right and bottom sides. As luck would have it, I happened across a great 

[triangle SASS mixin][4] recently. It works by applying borders the full width of the object and where the borders intersect, they create a 45 degree line. Applying this to the front figure above, we get something like this: <pre class="codepen"><code></code><a href="http://codepen.io/donovanh/pen/KhtnL">Check out this Pen!</a></pre>   

## Setting the stage Once we have the shape in place, we'll need to place the back piece of our 3D triangle and position both pieces in 3D space. A containing "stage" div is needed to achieve this. We can apply a perspective value to the stage and any objects on it will be then drawn in relation to each other. [CodePen height=300 show=result href=Iuwge user=donovanh] The 3D positioning is achieved by using the 

[translateZ transform][5]. The front face is brought forward 80 pixels and the back face pushed back 80px. I also rotate the back panel 180 degrees on the Y axis so that it now faces out toward the back. The perspective value relates to the depth of the scene, and larger values produce more extreme distortion. Similar to this, the perspective-origin value is used to move the viewpoint. In this case, the viewpoint is about 200 pixels up, looking down and from off to the right side of the stage. 
## Creating the Sides With the front and back in place, we need to fill in the gaps on the sides. To do this, we'll use rectangular divs and position them: [CodePen height=300 show=result href=webct user=donovanh ] 

## Building the three triangles With one triangle done, it is straightforward to position three of them on the stage. Simply, copy the div called "triangle" above, and replicate it three times. I then add a class "one", "two" and "three" to each so they could be referenced individually: [CodePen height=300 show=result href=nlLFr user=donovanh ] 

## Styling the text To create the proper Zelda atmosphere, the right fonts are needed. I chose two fonts from the 

[Google Fonts][6] service. For the main font, [EB Garamond][7] seemed a decent choice. The smaller text looks good with [Old Standard TT][8]. Including these fonts involves referencing them in the head of your HTML: 
    <link href='http://fonts.googleapis.com/css?family=EB+Garamond' rel='stylesheet' type='text/css'>
    <link href='http://fonts.googleapis.com/css?family=Old+Standard+TT' rel='stylesheet' type='text/css'> Once they are included, you can then use them in your CSS: 

    h1
      font-family: "EB Garamond" The finishing touch was to add a border around the text: 

<pre class="codepen"><code></code><a href="http://codepen.io/donovanh/pen/ocrDl">Check out this Pen!</a></pre>

## Bringing it together To achieve the animation I actually created several different animations and timed them so that they all worked together. Using keyframe animation in CSS is made much easier by using SASS. I was even able to use a few basic mixins to make the process easier. I'm using Sass with the 

[Compass][9] framework, but to add support for animation and keyframes to Compass, you'll need [this gist][10]. **Animation 1: Rotating** The spinning effect is creating using the following keyframes: 
    @include keyframes(rotate)
      0%
      @include transform(rotateY(0))
      100%
      @include transform(rotateY(360deg)) To apply this animation to the figures, the following SASS is used (note: it makes use of the "animation" mixin 

[found here][10]): 
    figure
      @include animation(rotate 5s infinite linear) This calls the "animation" mixin and passes in the name of the keyframes (rotate), with a duration of 5 seconds, and "infinite" indicates that the animation will be repeated forever. It also specifies that the animation will use a "linear" easing, which maintains a constant speed throughout the animation. 

**Animation 2: Tumbling triangles** The introduction of the figures sees them tumble into place. This is done by transitioning them from an initial position offset to the top, left or right, while applying 360 degrees of rotation. This is achieved using the following keyframes: 
    @include keyframes(introduce-1)
      0%
        top: -200px
        opacity: 0
      45%
        top: -100px
        @include transform(rotateY(180deg) rotateX(-180deg))
        opacity: 1
      90%, 100%
        top: 0
        @include transform(rotateY(360deg) rotateX(-360deg)) By putting the ending position of the animation at both 90% and 100%, it ensures that the animation stays still for a moment at the end of it's tumbling. Applying both these to the figures at the same time looks like this: 

    @include animation(rotate 5s infinite linear, introduce-1 5s linear)

**Animation 3: Text** The text overlay is invisible at first, and I wanted it to wait until after the tumbling animation had finished before fading into place. To achieve this, we need to apply the following keyframes to the text: 
    @include keyframes(showZeldaText)
      0%, 80%
        @include transform(scale(0.6) translateZ(-100px))
        opacity: 0
        -webkit-filter: blur(2px)
      90%
        -webkit-filter: blur(0)
      100%
        @include transform(scale(1) translateZ(0))
        opacity: 1 For 90% of the time, the text is invisible. Then in the last 10% the text scales up from 60% scale to 100%, while the opacity changes from invisible to visible. Since the text animation starts at the same time as the tumbling, it needs a longer time to run so that it lines up properly. In this case, I set it to run for 6.5 seconds: 

    @include animation(showZeldaText 6.5s linear) With these in place, the last animation is the flash of background color that occurs when the tumbling stops. 

**Animation 4: Background flash** The background starts black, then when the tumbling finishes, briefly flashes white before the background image is shown. This colour sequence is also timed to coincide with the appearance of the text. Below are the keyframes that are used to create this effect: 
    @include keyframes(animate-background)
      0%
        background: #000
      83%
        background: #000
      84%
        background: #fff
      87%, 100%
        background: #000 A similar set of keyframes is used to show the background image after the screen flashes. 

## See it in action Here's a 

[live demo][3]. In my tests, it works reasonably well in recent versions of Chrome, Safari and Firefox. 
## Known Issues IE10 support is limited.There's also a bit of a glitch that seems to happen occasionally in all browsers where the tumbling animation on the elements doesn't always start. Anyone who might have suggestions on fixing that, I would appreciate it. 

## Have a go The HTML and CSS is 

[available on Github][11]. Please fork, share, and have a play with it. If you'd like to add to or fix something, submitting pull requests is a fast way to becoming one of my favorite people. Do it! I hope you enjoyed this post. I am more than happy to respond to questions and suggestions on Twitter at [@donovanh][12]. *This article was originally posted at <http://hop.ie/blog/zelda/>*

 [1]: /authors/donovan-hutchinson
 [2]: http://flippinawesome.org/wp-content/uploads/2013/04/zelda.png
 [3]: http://hop.ie/zelda
 [4]: http://minimalmonkey.com/sass-triangle-mixin/
 [5]: http://docs.webplatform.org/wiki/css/properties/transform
 [6]: http://www.google.com/fonts/
 [7]: http://www.google.com/fonts/specimen/EB+Garamond
 [8]: http://www.google.com/fonts/specimen/Old+Standard+TT
 [9]: http://compass-style.org/
 [10]: https://gist.github.com/donovanh/5426633
 [11]: https://github.com/donovanh/zelda
 [12]: http://twitter.com/donovanh