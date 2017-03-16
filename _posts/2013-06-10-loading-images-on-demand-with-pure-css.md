---
ID: 448
post_title: Loading Images on Demand with Pure CSS
author: admin
post_date: 2013-06-10 12:01:42
post_excerpt: ""
layout: post
permalink: >
  http://modern-web.dev/2013/06/10/loading-images-on-demand-with-pure-css/
published: true
dsq_thread_id:
  - "2809873648"
om_counter:
  - "263"
---
*By [Pavlo Pidlypenskyi][1]* The brain is a muscle, and as all muscles, it needs regular exercise to keep sharp, which is why I decided to take very old, but efficient, web optimization technique and implement it in new crazy way. You've most likely heard about [loading][2] [images][3] (and other resources) [on demand][4] – which is not only a common sense, but also a good way to speedup initial page load. Usually it is implemented via [JavaScript][5], but today I'll share with you how you can achieve [the same effect by using pure CSS][6]. 
## The problem First let's lay out the nature of the problem. Imagine we have a set of images to show to a user, but only some of them will be visible after initial page load. The rest of the images will sit below the fold and it's possible the user may never scroll down. So it makes sense to load those images after user start scrolling the page down. 

## Prevent images to load The first problem we need to solve is how to prevent the browser from loading all the images from start. I've tried several approaches here, but the one which has worked for me is to have images set as background images for some div elements and set its parent display: none. Almost 

[all modern browsers are smart enough][7] that they do not load background images for hidden elements. When we decide its time to load/show the image we will set the patent to display: block. 
## When to start loading images? Next let's think about what styles or CSS selectors are changed as the user scrolls to determine how we can catch the moment to start a particular image request. My first approach was to use position: sticky as it behaves like position: relative within its parent until a given offset threshold is met in the viewport (in other words, the user scrolled to some position). After this point element will start behave as position: fixed. Unfortunately, the surrounding elements don't note the change and hold their position and styling. Then I noted that user's cursor hovers different elements while scrolling the page. So, what if we put several page-wide div elements (with a class of .hoverer) on a page and start loading the image when the user hovers it? It may work, but there is no guarantee that user will hover all the div elements on first page so that we can load images on a second page. This lead me to the next idea – what if we load all images for first two pages from very beginning and put a div.hoverer on top of images on a second page. Once a user hovera it – we will load images for the third page. Third page will also contain div.hoverer for fourth page and so on. 

[<img class="alignnone size-full wp-image-450" alt="Images-Lazy-Load-Diagramm" src="http://flippinawesome.org/wp-content/uploads/2013/06/Images-Lazy-Load-Diagramm.png" width="826" height="764" />][8] The next question is: how can I determine what the size of the page is so that I can load first two screens of images initially? Unfortunately there is no way to do so, but I can make them big enough to fit majority of the screens (assuming all images are an equal, known in advance size). Examine the demonstration below [in order to get a feeling of how it will work ][9]: [iajsfiddle user="podlipensky" fiddle="J5fUu" height="300px" width="100%" show="css,html,result" skin="default"] 
## Keep images visible when the cursor has left Here is a problem: even if we show/load images when user hovers related div.hoverer, the image will disappear when user remove cursor from the div.hoverer. So how can we preserve the hover state? My first idea was to have div.hoverer elements overlapping each other, so that when you position the cursor over one element, it will be positioned over all elements we've hovered already. But the hover state is not propagated over the elements, only the top element will receive a hover state. Even 

[pointer-events: none][10] won't help us in this situation. What if I just delay appearance of display: none on image parent's element? I tried to use keyframes animation, but that didn't work out for me. Fortunately, transition does exactly what I need. Instead of another paragraph of explanation – [check out this demo][11]. [iajsfiddle user="podlipensky" fiddle="K4XKy" height="300px" width="100%" show="css,html,result" skin="default"] There is another problem in that we can't delay the display: block state as it is not animatable property in CSS, and transitions works only with animatable properties. Another dead end? Not exactly. I transition the width of the element and apply a media query to it. In media query I can specify the relationship between the element's width and its visibility. Oh, but it is not allowed to set [media query for particular HTML Element][12]; media queries are applicable to viewport only. This means that we have to put images into separate viewports (i.e. iframes). We can use [iframe.srcdoc][13] in order to avoid to having a separate URL for each image we are displaying. 
## Results and some comments Here is our 

[final result][6] (make your viewport height equal to 5 pumpkins in order to get the best experience). Because of need for  iframe.srcdoc support, the demo works in WebKit only, but you could make it work cross-browser if you put the actual source of the iframes into separate files. [<img class="alignnone size-full wp-image-449" alt="css_only_image_lazyload" src="http://flippinawesome.org/wp-content/uploads/2013/06/css_only_image_lazyload.png" width="1206" height="614" />][14] This is pretty long path to partially working solution. Would I even recommend to use it on production? No way. This is definitely not a best practice, but was an interesting task and some parts of it might be applicable in real-world web applications. I hope you had as much fun as I had, while working on this challenge. If you have any ideas of how to improve my approach – please do not hesitate to contact me. **Update:** It looks like there is a way to solve a problem with first two screens. I can have div element with a viewport size (by using height: 100vh;). The element will contain set of images wrapped into an iframe, as in my demo. The set should be big enough to fill the first two screens, and I will have a set of media queries to show different subsets of images depending on the viewport height. *This article was originally published at <http://podlipensky.com/2013/06/css-only-load-images-on-demand/>*

 [1]: /authors/pavlo-pidlypenskyi
 [2]: http://css-tricks.com/snippets/javascript/lazy-loading-images/
 [3]: http://luis-almeida.github.io/unveil/
 [4]: http://www.appelsiini.net/projects/lazyload
 [5]: http://podlipensky.com/tag/javascript/
 [6]: http://jsfiddle.net/podlipensky/TRj7E/17/
 [7]: http://timkadlec.com/2012/04/media-query-asset-downloading-results/
 [8]: http://flippinawesome.org/wp-content/uploads/2013/06/Images-Lazy-Load-Diagramm.png
 [9]: http://jsfiddle.net/podlipensky/J5fUu/
 [10]: https://developer.mozilla.org/en-US/docs/Web/CSS/pointer-events
 [11]: http://jsfiddle.net/podlipensky/K4XKy/
 [12]: http://www.jonathantneal.com/blog/thoughts-on-media-queries-for-elements/
 [13]: http://www.whatwg.org/specs/web-apps/current-work/multipage/the-iframe-element.html#attr-iframe-srcdoc
 [14]: http://flippinawesome.org/wp-content/uploads/2013/06/css_only_image_lazyload.png