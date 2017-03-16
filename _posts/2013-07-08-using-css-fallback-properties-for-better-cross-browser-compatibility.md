---
ID: 583
post_title: >
  Using CSS Fallback Properties for Better
  Cross-browser Compatibility
author: admin
post_date: 2013-07-08 12:53:32
post_excerpt: ""
layout: post
permalink: >
  http://modern-web.dev/2013/07/08/using-css-fallback-properties-for-better-cross-browser-compatibility/
published: true
---
*By [Afshin Mehrabani][1]* As you may know, Internet Explorer has supported something called conditional comments which allow you to include specific HTML or CSS based upon the result of a condition. Conditional comments in HTML first appeared in Microsoft's Internet Explorer 5 browser but it's been deprecated as of Internet Explorer 10. Internet Explorer has several problems with CSS, specially in IE6, 7 and 8. Web developers have used conditional comments to provide a better browser compatibility for Internet Explorer by often including some extra CSS files to fix bad behaviors and rendering mistakes. This works but it actually could be done in a better and simpler way. 
## CSS Basics Before going further, let's discuss a little about how CSS works. Suppose you have this code: 

    .me {
        color: #ccc;
    } In above example, we selected an element with class "me" and changed the color property to an hexadecimal value, "#ccc". Now look at the following code: 

    .me {
        color: #ccc;
        color: #000;
    } Because we changed the "color" property again to "#000" after setting it to "#ccc", the second value will be used and the color of text inside the element will be "#000". Ok, let's do something strange. I want to use an invalid CSS function to change the value: 

    .me {
        color: #ccc;
        color: boo(1);
    } Because 

`boo(1)` is not a valid CSS function, browsers don't change and replace the color value but rather keep the "#ccc" value for the color property. We can use this behavior of browsers to do something cool, as you'll see. 
## rgba() Again, go back to conditional comments; web developers use conditional comments to provide a better browser compatibility, especially in CSS3. In new technologies like CSS3, many of functions and methods are unavailable in older versions of IE. For example consider the 

`rgba()` function, a useful function to set color with an alpha transparency. [<img class="alignnone size-full wp-image-584" alt="rgba() browser support" src="http://flippinawesome.org/wp-content/uploads/2013/07/caniuse-rgba.png" width="949" height="370" />][2] *rgba() browser support* As you can see in the above chart from <a>CanIUse.com</a>, we can't use rgba() in IE 8 and older versions. What can we do to support this legacy browser? The first, commonly used way is using conditional comments to first include a modern browser CSS file and then include another CSS file inside a conditional comment for other versions of IE, like this: 
    <link href="modern.css" rel="stylesheet" />
    
    <!--[if lte IE 8]>
        <link href="ie8only.css" rel="stylesheet">
    <![endif]--> In modern.css we have: 

    .me {
        color: rgba(0, 0, 0, 0.5);
    } And in ie8only.css we have: 

<pre>.me {
    color: #ccc;
}</pre> When our users visits using Internet Explorer 8 or older, the browser renders the ie8only.css file and the color property is set to "#ccc". 

## CSS Fallback Properties However, we can do this better using CSS fallback properties within a single CSS file, like this: 

    .me {
        color: #ccc;
        color: rgba(0, 0, 0, 0.5);
    } You may be able to guess what will happened by using above code. When you set the color property to "#ccc," because it's a valid value in all browsers, it works without any problem. In the next line we used the rgba() function. In modern browsers, because it's a valid function, it works without any problem and the browser uses the second value as the color property. But in IE 8 or older versions, because it's an invalid value, the browser does nothing and still uses the first value, "#ccc." What we've done is use the CSS fallback properties technique: when a function or value is invalid, thebrowser uses the last available value for that property. With this technique, you don't need to create two separate files or write confusing conditions in the HTML files. Also your application doesn't need to send two separate HTTP requests, first for modern CSS file and then for IE fix file. You can use this technique in many situations and I believe it's a better approach than conditional comments. 

*This article was originally published at <http://afshinm.name/css-fallback-properties-better-cross-browser-compatibility/>*

 [1]: /authors/afshin-mehrabani
 [2]: http://flippinawesome.org/wp-content/uploads/2013/07/caniuse-rgba.png