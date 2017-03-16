---
ID: 969
post_title: 'Break The Wrist And Walk Away: Responsive Design And Bootstrap 3'
author: admin
post_date: 2013-09-16 12:03:33
post_excerpt: ""
layout: post
permalink: >
  http://modern-web.dev/2013/09/16/break-the-wrist-and-walk-away-responsive-design-and-bootstrap-3/
published: true
sidebar_value:
  - Default Sidebar
sidebar_position_value:
  - right
dsq_thread_id:
  - "2809890323"
om_counter:
  - "622"
---
*By [Burke Holland][1]* http://www.youtube.com/watch?v=Lcw6xBaCaXM This is me and design. I want to be a designer. I want to build beautiful sites. I don't just want to code, I want to be an artist. A creative. I work for a UI company. Everything that I live and breathe is UI. The sad truth of the matter is that I am not a designer, and no amount of trying or wanting is going to make me one. Just like Kip though, I refuse to give up on my dream. It does mean though, that I need help. I need all the help I can get. Also, just like Kip, even with all the help in the world, I still will never be able to build truly beautiful and original sites. I will never be a cage fighter. And that's OK. If high school drilled one thing into us, it's that you have to know who you are and fully support that person. That and how to make a beer bong. However, this means that I need help when it comes to CSS, and a lot of it. This is why I love <a href="http://getbootstrap.com/2.3.2/" target="_blank">Twitter Bootstrap</a>. 
## Responsive Design Basics {#responsive-design-basics}

<a href="http://alistapart.com/article/responsive-web-design" target="_blank">Responsive design</a> is the concept that instead of your web page looking great on a desktop and really tiny on a mobile phone, content is rearranged and kept large so that instead it looks like a website that was written specifically for a mobile device. Responsive design is generally broken up into three different conceptual areas… 
1.  Fluid Grids
2.  Fluid Media
3.  Media Queries Bootstrap 3 handles all three of these areas for you on some level, providing you with a solid base on which to build your responsive application. 

## Bootstrap Basics {#bootstrap-basics} Before we get into the guts of Bootstrap 3 and how it wrangles responsive design for you, lets first take a look at the groundwork that Bootstrap has laid for you with it's typography and base CSS resets. 

### Typography {#typography} In this example, you see the various headings (h1 - h6), and a paragraph tag. Notice that there is no left or right padding or margin on the text at all. 

<a href="http://jsbin.com/evifiQA/1" target="_blank">View Demo In New Window</a> If you toggle bootstrap off, you see how things change. The text is ugly for one, using the default browser style. WebKit based browsers will also add a `-webkit-margin-before` and `-webkit-margin-after` CSS class that adds some padding to the text. Also, the size of the text is changed. If you change the font size on the body to say, 20px, you can see that the browser resizes all the text. 
    body {
      font-size: 20px;
    }
     At its core, Bootstrap includes normalize.css. This is a library that does a “reset” on your CSS. In other words, it provides a baseline for font, margin and padding that assures your simple HTML will look the same in all browsers, as well as fix some basic browser bugs. Note that in most modern browsers, normalize is almost unnecessary. That's encouraging. 

### Media Queries {#media-queries} Let's start by doing a brief examination of media queries. Media queries are one of the 3 core pieces of the responsive design puzzle, but you can't really understand the other two if you don't get this one. A media query is 

[defined by MDN][2] in this rather verbose but precise description. 
> A media query consists of a media type and at least one expression that limits the style sheets' scope by using media features, such as width, height, and color. Media queries, added in CSS3, let the presentation of content be tailored to a specific range of output devices without having to change the content itself. I overly simplify it to just this. 
> Conditionally applying CSS based on the size of the browser window. Media queries can be used to load in entire stylesheets... 
    <!-- CSS media query on a link element -->
    <link rel="stylesheet" media="(max-width: 800px)" href="example.css" />
     But they are more commonly found inline in stylesheets loaded by the browser… 

    <!-- CSS media query within a style sheet -->
    <style>
      @media tv and (min-width: 700px) and (orientation: landscape) {
        .facet_sidebar {
          display: none;
        }
      }
    </style>
     As you can see from this fancy pants media query for a tv, media queries consist of two parts - a media type and a size rule that can have multiple conditions. Media queries can get unwieldy. Most often, you will not see the media type. Usually responsive design is done based on the size of the screen, not the media type that the device reports itself to be. A device may lie about its identity, but it cannot lie about it's screensize. Let's take a look at a simple media query in action. 

<a href="http://jsbin.com/IsOyUfo/1" target="_blank">View Demo In New Window</a> This media query says that when the width of the screen gets below 700 pixels, change the color of the text to “gentleman's pink” - otherwise known as Salmon. This is done by setting a `max-width`, which effectively says “This is the maximum width at which the following CSS rule applies”. You can apply any CSS like this, including transitions - where supported; like fading the text out using a transition. Try changing the CSS above to the following and then resize the browser window. The gray box displays the current width of the window. Notice that the CSS is effectively removed when the media query's conditions are not met. 
    h1 {
      -webkit-transition: opacity 1s;
      -o-transition: opacity 1s;
      -mos-transition: opacity 1s;
      transition: opacity 1s;
    }
    
    @media (max-width: 400px) {
      h1 {
        opacity: 0;
      }
    }
     We're almost ready to get into Bootstrap itself and talk about how you use it to build gorgeous responsive sites. Before we do though, a word from the legal department of the web. 

## Disclaimer {#disclaimer} Bootstrap is not magic. It does not cure cancer. Only Chuck Norris' tears do that. You are not going to drop Bootstrap onto your site and magically have a cross platform application that works on every device and is future proof. In fact, you are going to have to do a fair bit of extra work on top of what Bootstrap gives you out of the box to be fully responsive. Also, keep in mind that Responsive Design is really just the beginning when it comes to building web experiences on mobile devices. Today's phones and tablets are capable of so much more than just laying out content with a font big enough for you to read it. We are still trying to figure out how to simply display the content for these devices, but they are poised to do so much more if we can leverage all of the power they afford to users above and beyond what a desktop was ever capable of. Appropriate disclaimers in place, lets dig in. 

## Bootstrap 3 {#bootstrap-3} Before you do ANY responsive design, you need to add a meta tag to your page. All of the bootstrap in the world won't do you a lick of good if you leave this tag out. 

    <meta name="viewport" content="width=device-width, initial-scale=1.0"> What does this tag do? When Apple first came up with a phone that could actually browse the internet, they had a huge problem to solve. Namely, how on earth do you browse the internet on such a tiny screen? The solution? Pan and zoom. Because the iPhone did touch, did multi-touch and did all of it so well, Apple decided to have the browser treat pages as if it were a desktop browser. By default, the device will zoom all the way out on any page so that you can see the entire site. It's then up to you to double tab to zoom in. This was an enormously effective strategy, and as it turns out, people don't mind tapping and zooming. But we can do better. Using a viewport meta tag means that your page will be automatically zoomed rather than using the default width, which is usually 980px wide. 

### Mobile First {#mobile-first} Bootstrap has always had an emphasis on mobile. However, up until version 3, mobile was a sort of “bolt-on” addition. In fact, the responsive styles were in a separate stylesheet that you could choose not to include. As of Bootstrap 3, this has completely changed. Bootstrap is now mobile first. This means that you are expected to be building a mobile app first, and special considerations are made to accommodative larger screens, not the other way around as it has been for quite some time. This means that there has been a fundamental change in the grid system. If you knew the grid system in Bootstrap 2, it's going to require you to reacquaint yourself with the new “mobile first” Bootstrap 3 grid. 

### Bootstrap 3 Fluid Grids {#bootstrap-3-fluid-grids} Bootstrap is still comprised of a container that - well - “contains” rows. Each row contains 1 - 12 columns that behave differently depending on the CSS class you choose to apply. You used to be able to choose between a fixed and fluid container. Now there is only the fluid container. The container centers the content on the page, providing equal spacing on the left and the right. Containers have a max width set at various breakpoints inside of Bootstrap. 

<a href="http://jsbin.com/enUrAJ" target="_blank">View Demo In New Window</a> You can resize that example to see the container change size at different points. Notice that the grid has a max width of `1140px`. Once the width is reduced below `1200px`, the grid width is reduced to `940px`. Once we break `992px` on the window, the container drops to a max width of `720px`. The padding is always at least `15px`, and then set to auto on anything greater than `768px` - perfectly centering the container on the page. The breakpoints are important to know, because they are going to tell you which point you grid is going to change it's layout, based on which column types you choose. That's a bit confusing, so let's just look at the media queries that Bootstrap has first. 
### Bootstrap 3 Media Queries {#bootstrap-3-media-queries} Bootstrap divides up the layout into 4 categories, phones - or anything 400 pixels or larger. Tablets, which are 768 pixels or larger, medium sized devices - enormous tablets or desktops, and large devices, like giant cinema displays. 

    /* Extra small devices (phones, up to 480px) */
    /* No media query since this is the default in Bootstrap */
    
    /* Small devices (tablets, 768px and up) */
    @media (min-width: @screen-sm) { ... }
    
    /* Medium devices (desktops, 992px and up) */
    @media (min-width: @screen-md) { ... }
    
    /* Large devices (large desktops, 1200px and up) */
    @media (min-width: @screen-lg) { ... }
    
    *Source: [Bootsrap 3 Docs / CSS / Grid system / Media queries](<a href="http://getbootstrap.com/css/#grid-media-queries">http://getbootstrap.com/css/#grid-media-queries</a>)*
     Based on these media queries, Bootstrap 3 provides you with 4 different types of columns. Each of these columns resides in a 

`row`, and the entire layout resides in a singular `container` classed element. You may have a maximum of 12 columns in any row. Lets look at the four different column types. 
#### col-xs {#col-xs} The first type is based on the first media query and is targeted at phones. These columns never stack or break. They are always horizontal and will continue to collapse as much as the viewport does. They also have 

`15px` of left and right padding as you can see from the example below. <a href="http://jsbin.com/efoxAz/1" target="_blank">View Demo In New Window</a> 
#### col-sm {#col-sm} The second type is the the small column, abbreviated 

`col-sm`. It stays horizontal at anything above `768px` and breaks below that to a stacked layout. <a href="http://jsbin.com/EGIwETe/1" target="_blank">View Demo In New Window</a> 
#### col-md {#col-md} The medium column is stacked at anything below 

`992px`, and is intended for a smaller desktop layout. <a href="http://jsbin.com/OnUwOsi/2" target="_blank">View Demo In New Window</a> 
#### col-lg {#col-lg} The last type is the 

`col-lg` column, which is stacked below `1200px`. On really big screens it's horizontal, but stacked everywhere else. <a href="http://jsbin.com/oGuQALa/1" target="_blank">View Demo In New Window</a> 
#### Why Four Column Types {#why-four-column-types} So why do we need for column types? Didn't the previous 1 responsive column type cut it? On their own, each of these columns types is relatively identical in it's behavior (except the xs columns). You get below a certain resolution and it stacks. The answer is really choice. Having four column types gives you control of exactly how your layout behaves at the 4 major decision points as defined by Bootstrap. If all of your columns immediately stack at anything below desktop size, you are lumping all mobile devices in together, and that's not fair. Many tablets have a resolution that's not quite big enough to handle a full desktop site, but a mobile experience feels like a “Fisher Price” UI. Having four columns enables you to build that UI that is “right for the resolution”, without having leave anyone out. 

#### Offsets {#offsets} Bootstrap offers some additional classes to help you with precise positioning. You can pad a column left by a specified number of columns using an offset. Note that this only works for 

`col-sm-*` and larger column types. Offsets are not respected on the `col-xs-*` layout. <a href="http://jsbin.com/oduKObO/1" target="_blank">View Demo In New Window</a> 
#### Push Me, Pull You {#push-me-pull-you} Grids also allow you to push and pull columns. This lets you to re-order the columns based on their class, not their position in the DOM. On first glance, this might seem like a bad idea, but again, this all about choice. It allows you to reorder your content based on the viewport size. You may want to do this in the case that you have certain content that is important and gets pushed below the fold on smaller screens. For example, you have have a 2 column layout on desktop, you may want a very important image on the right and the text on the left. However, on mobile, you want it to stack on top of the text because it's more important to you to have the user see the image if nothing else. You shouldn't have to be forced to put it on the left for desktop just to achieve a higher stack position on mobile. Pushing a column moves it right, and pulling it moves it left. 

<a href="http://jsbin.com/OvUJUva/2" target="_blank">View Demo In New Window</a> Again, these classes are not available for the smallest `col-xs-*` sizes. This is because it is mobile first. Your base design is the smallest, and you only rearrange content as the screen gets bigger. This is *hard* to wrap your head around when you first start with Bootstrap 3, but once it sinks in, things really start to fall into place. 
## Fluid Media {#fluid-media} The last thing for us to discuss in terms of the three pieces of responsive design is responsive media. As far as what Bootstrap covers, this refers to images and not video. Responsive video is a scenario not covered out-of-the-box by Bootstrap since it generally requires some JavaScript trickery. 

### Images {#images} Lets look at the way an image behaves on the web. Usually you know the size of your images before they are loaded into the page. As a general rule of thumb, you want to specify their width and height when possible. Otherwise the browser has no way of knowing what the image dimensions are until the image loads, and therefore no spot on the page is reserved for it. This will cause your layout to jump. Have a look at a kitty image which has a fixed size of 400 by 400 pixels. In this example, I have added a fixed height and width inline on the element. Typically, your images should have a specified width and height if you know it ahead of time. Notice though that the image is too large for the viewport, and while the text wraps in it's container, the images stays at it's fixed size. 

<a href="http://jsbin.com/AKANIXO/3/" target="_blank">View Demo In New Window</a> What we want is for the image to resize itself as it's container shrinks. As it turns out, making images fluid is pretty easy. All you have to do is add a `width` of 100% and set the `height` to `auto`. Done. Bootstrap does this for you by use of the `img-responsive` tag. This is another change from Bootstrap 2. All images used to be responsive when the responsive CSS was added. Now you get to toggle that on and off with just a class. Some of the other Bootstrap 3 utility classes for images will make your image responsive by default. For instance, applying the `img-thumbnail` class for an image thumbnail makes your image responsive as well. Using rounded corners or the `img-circle` class still requires use of the `img-responsive` class to have the image effect *and* a still get a responsive image. 
## Responsive Sugar {#responsive-sugar} As of right now, we have discussed how Bootstrap 3 addresses fluid grids, fluid media and media queries. If it left you there and provided nothing else, this would be a great framework for navigation and conditionally showing and hiding content. The Bootstrap 3 creators know that, and have included a bit of responsive design ‘sugar' to really jump start your app. 

### Collapsible Navbars {#collapsible-navbars} The responsive navbar is the defining feature of Bootstrap. I'm not going to lie. I love it. Figuring out how to adapt your main navigation to smaller screens is a giant obstacle to your success, and Bootstrap effectively negates this with the use of the collapsible navbar. That navbar that we all love so much stays much the same in this release. Some of the classes have changed, but it's largely the same concept. I think we all know what a Bootstrap 3 navbar is, and I highly doubt you need to see it again, but here it is in all it's glory. 

<a href="http://jsbin.com/iXukEci/1" target="_blank">View Demo In New Window</a> A couple of notes on the navbar: 
*   You must have the Bootstrap JavaScript collapse plugin in order for the responsive features to work
*   You don't have to have the navbar at the top! You can [fix it to the bottom too][3]. True story.
*   You should add a `data-role="navigation"` to every navbar if you want keyboard navigation (accessibility).
*   You can tweak a navbar so it collapses at a different breakpoint than what Bootstrap specifies

### Responsive Utility Classes {#responsive-utility-classes} Bootstrap still comes with a set of handy classes for toggling the visibility of elements at its four specified breakpoints. The following example has four icons: a phone; a tablet; a laptop; and a desktop. Each of these icons is assigned Bootstrap 3 utility classes so that it is only visible at it's corresponding size. For instance, the phone icon in the demo looks like this: 

    <i class="icon-mobile-phone" .visible-xs></i> Note that if you specify 

`visible-xs` on an element, Bootstrap will automatically hide it for you at any other breakpoints. If you want it visible again at a breakpoint, you need to add in a visibility class for that size. <a href="http://jsbin.com/eMolIk/1" target="_blank">View Demo In New Window</a> 
## Bootstrap 3 Is A Better Bootstrap {#bootstrap-3-is-a-better-bootstrap} After working with Bootstrap 3 and getting over the changes, I really like what they have done. Bootstrap 2 was incredibly comprehensive. Bootstrap 3 takes that all encompassing framework and adds in very granular control for laying out elements at different resolutions. My favorite feature of all though, is the way that Bootstrap 3 is nudging us towards building for mobile devices before we build for desktops. It's subtle, but it will change the way you think without you even realizing it.

 [1]: /authors/burke-holland/
 [2]: https://developer.mozilla.org/en-US/docs/Web/Guide/CSS/Media_queries
 [3]: http://getbootstrap.com/components/#navbar-fixed-bottom