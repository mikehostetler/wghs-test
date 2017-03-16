---
ID: 1081
post_title: Playing with CSS Regions in Edge Reflow
author: admin
post_date: 2013-10-14 11:35:19
post_excerpt: ""
layout: post
permalink: >
  http://modern-web.dev/2013/10/14/playing-with-css-regions-in-edge-reflow/
published: true
dsq_thread_id:
  - "2809887418"
om_counter:
  - "28"
---
*By [Joan Lafferty][1]* Kids spend most of their awake hours just playing. This helps them to understand the world and figure out how things work. In fact, the picture above is of my son playing with paint and learning about colors. With Edge Reflow's latest public release, we are giving you the opportunity to **play**, learn, and use an emerging CSS feature:  [CSS Regions][2]. CSS Regions will allow your content to flow through different containers on your web site. This enables you to build more magazine-style layouts. As of this month, CSS regions are supported in Safari on iOS7. You can also view CSS Regions in Chrome by enabling the [experimental web platform features flag][3]. [<img class="alignnone size-full wp-image-1083" src="http://flippinawesome.org/wp-content/uploads/2013/10/reflow_regions.jpg" alt="reflow_regions" width="613" height="147" />][4] Edge Reflow is the first tool that will allow you to see CSS Regions in action without writing any code. Then, after drawing a few regions, you can view the HTML/CSS output to see how the magic happened. 
## Getting Started with CSS Regions in Reflow Let's get started. The first thing you will need is Edge Reflow CC which you can download from 

[here][5]. I will take you through a simple CSS Region example and I hope you will continue to play with the feature and use it in the future. 
### Step #1: Turn on CSS Regions support in Reflow by going to the menu item View -> Shiny Web Features < 

[<img class="alignnone size-full wp-image-1084" src="http://flippinawesome.org/wp-content/uploads/2013/10/reflow_regions2.jpg" alt="reflow_regions2" width="490" height="278" />][6] This will bring up a dialog box describing some new CSS features including CSS Filters and CSS Regions, which are not supported in all browsers. You will need to ensure that CSS Regions is on and then press OK and dismiss the dialog. 
### **Step #2: Draw a text element on the canvas. Copy and Paste a lot of text in it.** I've copied my text from "The Atlantic" which always promises a good, but, long read but, if you prefer, yo can just paste some 

[lorem ipsum text][7]. 
### **Step #3: Right-click on the text element and choose the menu item "Create Region Container".**

[<img class="alignnone size-full wp-image-1085" src="http://flippinawesome.org/wp-content/uploads/2013/10/reflow_regions3.jpg" alt="reflow_regions3" width="650" height="344" />][8] 
### **Step #4 In the flow container, look for the + icon on the bottom right. Click on this and then draw out other flow containers.** Play with resizing the various containers and notice that all of your text content will flow from one of these containers to another. Also play with the size of your canvas by dragging the canvas handle right and left. 

[<img class="alignnone size-full wp-image-1086" src="http://flippinawesome.org/wp-content/uploads/2013/10/reflow_regions4.jpg" alt="reflow_regions4" width="650" height="464" />][9] Note, when the + icon is red, this means that there is more content overflowing the current container. 
### **Step #5: Edit your content.** If you want to edit the original content that you are having flow through the various flow containers, double click on any of the containers. From this "content editing mode", you can add images, more text, add boxes, and change styles as you sit fit. All of the content will flow through the containers that your drew previously. 

[<img class="alignnone size-full wp-image-1087" src="http://flippinawesome.org/wp-content/uploads/2013/10/reflow_regions5.jpg" alt="reflow_regions5" width="650" height="314" />][10] 
### **Step #6: Exit content editing mode by clicking on the left arrow in the status bar.**.. ...or press ESC, or double click on the checkered portion of the editing area. 

### **Step #7: See the CSS code written out for the container that you drew.** To do this, ensure that the container is selected. Then, click the <> icon in the bottom status bar. 

[<img class="alignnone size-full wp-image-1088" src="http://flippinawesome.org/wp-content/uploads/2013/10/reflow_regions6.jpg" alt="reflow_regions6" width="420" height="221" />][11] 
### **Step #8 Preview in Chrome.** From Reflow, you can preview your design in the browser. This can be done from the View menu. If you don't have the correct version of Chrome, you will get a warning. 

### Step #9 View all the HTML and CSS code. In step 8, you viewed the design in the browser, so, there must be HTML and CSS, right? Of course there is. You can find the CSS and HTML that Edge Reflow creates for your design inside the assets directory of your project. Keep in mind that Reflow is not intended as a code generation tool, but reviewing the HTML and CSS can give you a sense of how to build the final project source. 

[<img class="alignnone size-full wp-image-1089" src="http://flippinawesome.org/wp-content/uploads/2013/10/reflow_regions7.jpg" alt="reflow_regions7" width="445" height="157" />][12] Regions have the opportunity to really change the way web sites lay out their content. What I've built above is not beautiful, but, its just a sample of how your content can flow through any part of your page you choose. To download the simple Reflow project I talked about above, you can find it [here][13]. *This article was originally published at <http://butterfliesandbugs.wordpress.com/2013/09/24/using-edge-reflow-to-play-with-css-regions/>*

 [1]: /authors/joan-lafferty
 [2]: http://html.adobe.com/webplatform/layout/regions/
 [3]: //flags/#enable-experimental-web-platform-features
 [4]: http://flippinawesome.org/wp-content/uploads/2013/10/reflow_regions.jpg
 [5]: http://creative.adobe.com/products/reflow
 [6]: http://flippinawesome.org/wp-content/uploads/2013/10/reflow_regions2.jpg
 [7]: http://www.lipsum.com/
 [8]: http://flippinawesome.org/wp-content/uploads/2013/10/reflow_regions3.jpg
 [9]: http://flippinawesome.org/wp-content/uploads/2013/10/reflow_regions4.jpg
 [10]: http://flippinawesome.org/wp-content/uploads/2013/10/reflow_regions5.jpg
 [11]: http://flippinawesome.org/wp-content/uploads/2013/10/reflow_regions6.jpg
 [12]: http://flippinawesome.org/wp-content/uploads/2013/10/reflow_regions7.jpg
 [13]: https://www.dropbox.com/s/1qhxv5lcl1vbnqq/SimpleRegions.zip