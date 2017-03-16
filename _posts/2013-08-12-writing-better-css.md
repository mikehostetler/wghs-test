---
ID: 826
post_title: Writing Better CSS
author: admin
post_date: 2013-08-12 11:59:58
post_excerpt: ""
layout: post
permalink: >
  http://modern-web.dev/2013/08/12/writing-better-css/
published: true
dsq_thread_id:
  - "2809886864"
sidebar_value:
  - Default Sidebar
sidebar_position_value:
  - right
om_counter:
  - "160"
---
*By [Mathew Carella][1]* Writing good CSS code can speed up page rendering. Essentially, the fewer rules the engine has to evaluate the better. [MDN][2] groups CSS selectors in four primary categories, and these actually follow the order of how efficient they are: 
1.  ID Rules
2.  Class Rules
3.  Tag Rules
4.  Universal Rules The efficiency is generally quote from Even Faster Websites by Steve Souders which was published in 2009. Souders list is more detailed, though, and you can find the 

[full list referenced here][3]. You can find more details in [Google's best practices for efficient CSS selectors][4]. In this article I wanted to share some simple examples and guidelines that I use for writing efficient and performant CSS. This is inspired by, and follows a similar format to, [MDN's guide to writing efficient CSS][2]. 
## Don't Overqualify As a general rule, don't supply more information than is necessary. 

    // bad
    ul#someid {..}
    .menu#otherid{..}
    
    // good
    #someid {..}
    #otherid {..}

## Descendant Selectors are the Worst Not only is this not performant but it is fragile, as changes to the HTML can easily break your CSS. 

    // very bad
    html div tr td {..}

## Avoid Chaining This is similar to overqualifying and it is preferable to simply create a new CSS class selector. 

    // bad
    .menu.left.icon {..}
    
    // good
    .menu-left-icon {..}

## Stay KISS Let's imagine we have a DOM like this: 

    <ul id="navigator">
        <li><a href="#" class="twitter">Twitter</a></li>
        <li><a href="#" class="facebook">Facebook</a></li>
        <li><a href="#" class="dribble">Dribbble</a></li>
    </ul> Following upon the prior rules... 

    // bad
    #navigator li a {..}
    
    // good
    #navigator {..}

## Use a Compact Syntax Whenever possible, use the shorthand syntax. 

    // bad
    .someclass {
     padding-top: 20px;
     padding-bottom: 20px;
     padding-left: 10px;
     padding-right: 10px;
     background: #000;
     background-image: url(../imgs/carrot.png);
     background-position: bottom;
     background-repeat: repeat-x;
    }
    
    // good
    .someclass {
     padding: 20px 10px 20px 10px;
     background: #000 url(../imgs/carrot.png) repeat-x bottom;
    }

## Avoid Needless Namespacing

<pre>// bad
.someclass table tr.otherclass td.somerule {..}

//good
.someclass .otherclass td.somerule {..}</pre>

## Avoid Needless Duplication Whenever you can, combine duplicate rules. 

    // bad
    
    .someclass {
     color: red;
     background: blue;
     font-size: 15px;
    }
    
    .otherclass {
     color: red;
     background: blue;
     font-size: 15px;
    }
    
    // good
    
    .someclass, .otherclass {
     color: red;
     background: blue;
     font-size: 15px;
    }

## Condense Rules When You Can Following on the prior rule, you can combine duplicate rules but still differentiate classes. 

    // bad
    .someclass {
     color: red;
     background: blue;
     height: 150px;
     width: 150px;
     font-size: 16px;
    }
    
    .otherclass {
     color: red;
     background: blue;
     height: 150px;
     width: 150px;
     font-size: 8px;
    }
    
    // good
    .someclass, .otherclass {
     color: red;
     background: blue;
     height: 150px;
     width: 150px;
    }
    
    .someclass {
     font-size: 16px;
    }
    
    .otherclass {
     font-size: 8px;
    }

## Avoid Unclear Naming Conventions It is preferable to use semantic names. A good CSS class name should describe what it is about. 

## Avoid !importants When possible, you should instead use good qualified selectors. 

## Follow a Standard Declaration Order While there are 

[a number of common ways][5] to order CSS properties, this is a commonly used one that I follow. 
    .someclass {
     /* Positioning */
     /* Display & Box Model */
     /* Background and typography styles */
     /* Transitions */
     /* Other */
    }

## Format Your Code Properly Code that is easier to read is easier to maintain. Here's the format I follow: 

    // bad
    .someclass-a, .someclass-b, .someclass-c, .someclass-d {
     ...
    }
    
    // good
    .someclass-a, 
    .someclass-b, 
    .someclass-c, 
    .someclass-d {
     ...
    }
    
    // good practice
    .someclass {
        background-image:
            linear-gradient(#000, #ccc),
            linear-gradient(#ccc, #ddd);
        box-shadow:
            2px 2px 2px #000,
            1px 4px 1px 1px #ddd inset;
    }

## Where to Go From Here Obviously these are just a handful of rules that I try to follow in my own CSS to make it both more efficient and easier to maintain. If you want to read more on the topic, I suggest reading 

[Writing Efficient CSS][2] on MDN and Google's guide to [optimize browser rendering][4]. *This article was originally published at <http://blog.mathewdesign.com/2013/07/04/writing-performant-and-quality-css/>*

 [1]: /authors/mathew-carella
 [2]: https://developer.mozilla.org/en-US/docs/Web/Guide/CSS/Writing_efficient_CSS
 [3]: http://csswizardry.com/2011/09/writing-efficient-css-selectors/
 [4]: https://developers.google.com/speed/docs/best-practices/rendering#UseEfficientCSSSelectors
 [5]: http://css-tricks.com/new-poll-how-order-css-properties/