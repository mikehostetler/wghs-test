---
ID: 869
post_title: Responsive Content Using CSS Regions
author: admin
post_date: 2013-08-19 11:27:47
post_excerpt: ""
layout: post
permalink: >
  http://modern-web.dev/2013/08/19/responsive-content-using-css-regions/
published: true
---
*By [Alan Greenblatt][1]* CSS Regions allow content to flow across multiple areas of the display, called regions.  The beauty of CSS Regions is that you can separate the content from the layout.  And with that comes the ability to create some responsive content very easily. 
## flow-into and flow-from The 

`flow-into` CSS property can be used to prevent some content from displaying normally, but rather have its display redirected to a *named flow*: 
    #source {
       -webkit-flow-into: main-thread;
       flow-into: main-thread;
    } Then we can specify our layout, and tell the layout where to get its content from using the 

`flow-from` CSS property: 
    .region {
       -webkit-flow-from: main-thread;
       flow-from: main-thread;
    } In the case above, all of the content in the element with ID 

*source* will actually get rendered into the layout specified by all of the elements of class *region*. 
## Example Let's take a look at an example.  First, make sure you are viewing this with either a Webkit nightly, Chrome Canary or an up-to-date Chrome Stable.  Then, if you haven't already done so, you need to enable CSS regions by navigating to 

[chrome://flags][2] and clicking on "Enable" under *Enable experimental Webkit features*.  You'll then need to relaunch the browser. [CodePen height=450 show=css href=rdLHg user=awgreenblatt] I tried to keep the example as simple as possible.  Basically, we have a `div` whose ID is *source*.  That `div` contains all of the text to be displayed.  But, since we have the following in the CSS... 
    #source {
     -webkit-flow-into: main-thread;
     flow-into: main-thread;
    } ...all of the text in that 

`div` gets redirected into the named flow called *main-thread* and doesn't display in its original `div`. We also have several `div`s, all of class *region*.  In the CSS we have: 
    .region {
     width: 150px;
     height: 150px;
     float: left;
     margin: 10px 5px;
     -webkit-flow-from: main-thread;
     flow-from: main-thread;
    } All of those region 

`div`s will get their content from the *main-thread* named flow.  Since they are all `float: left`, the `div`s will automatically re-layout as you adjust the width of the window. Finally, I stuck an image in the middle of it just for fun to show that your regions can be easily interspersed with other content. Try that now. Make this window narrower and wider, and you should see the text automatically adjust. 
## Conclusion Now think how much work would be involved in getting this simple example working without CSS Regions. You can find out more about 

[CSS Regions here][3]. *This article was originally published at <http://blattchat.com/2013/08/16/responsive-text-using-css-regions/>*

 [1]: /authors/alan-greenblatt
 [2]: //flags/
 [3]: http://html.adobe.com/webplatform/layout/regions/