---
ID: 331
post_title: Web Design Tips for Developers
author: admin
post_date: 2013-05-13 12:13:33
post_excerpt: ""
layout: post
permalink: >
  http://modern-web.dev/2013/05/13/web-design-tips-for-developers/
published: true
---
*By [Alex Grande][1]* Think of this article as a general guideline for designing any website. I've learned these lessons through photography, working in the Enlightenment Linux window manager many years ago, and developing for a design agency. I apply these rules while designing and building [Recognize][2], a social employee appreciation platform. Being able to do both has saved our team a lot of time and money. Learn to design along side your web development skills, and your value greatly increases. [<img class="alignnone size-full wp-image-332" src="http://flippinawesome.org/wp-content/uploads/2013/05/Enlightenment_2-1024x819.jpg" alt="Enlightenment_2-1024x819" width="1024" height="819" />][3] *Enlightenment window manager on Linux* 
## Follow nature If you have to follow one rule, this is it. Later in this article I will go through more specific examples of this, such as the grid, 1.6 golden ratio, and following your senses. In the meantime, try to think more theoretical. Think about Apple, and look at their web design and applications. The reflections, subtle patterns, and momentum scrolling are all patterns taken from nature. Observe how light and objects behave, then mimic that in your design. 

[<img class="alignnone size-full wp-image-333" src="http://flippinawesome.org/wp-content/uploads/2013/05/iphone-gesture-300x288.jpg" alt="iphone-gesture-300x288" width="300" height="288" />][4] 
## Take advantage of the senses Users will appreciate sensory experiences in your application. Tactile, visual, and auditory are all ways to stimulate your users. 

**Button example for visual and tactile experience** For instance, a button should suppress, or light up, when pressed. Next time you get in a car, press a few buttons. Try to mimic those behaviors in your application, and you'll create a more realistic user experience. **Find ways to use vibrations to simulate tactile response** Android innovates with vibration responses. Most people say that their Android's vibration response gives them a sense of context when a keyboard button is pressed on the screen. Android's vibration is like iPhone's momentum natural scrolling in that they are both game changing user experiences. Think about how you can innovate with vibrations. **Are sounds possible for your application?** Auditory experiences are generally considered a faux pas in desktop applications. Most apps steer clear and tend to indicate events through visual indicators, like updating the title tag. Still, audio if done right can add value to your desktop app. But mobile is a different ballgame. When a push notification triggers a background service, play a sound to indicate a message or event. Give yourself a voice, and allow the user to know when an event occurs with a unique sound. Remember ICQ and their weird "ho-hoo" jingle? 
## Whitespace is your best friend Some try to fill empty screen real estate with pictures and useless text. Don't make this mistake. Apple is again another great example, they place the product in the middle of the screen and leave empty space around it. If you properly take advantage of whitespace, you'll find people will be at ease with your design. Whitespace helped Google beat Yahoo. Google put the primary action front and center with nothing around it to distract the user. Embrace whitespace and start allowing your users to focus. 

[<img class="alignnone size-full wp-image-334" src="http://flippinawesome.org/wp-content/uploads/2013/05/Screen-Shot-2013-04-26-at-2.01.52-PM-1024x762.png" alt="Screen-Shot-2013-04-26-at-2.01.52-PM-1024x762" width="1024" height="762" />][5] 
## Focus focus focus

[<img class="alignnone size-full wp-image-335" src="http://flippinawesome.org/wp-content/uploads/2013/05/jpeg.jpeg" alt="jpeg" width="640" height="480" />][6] *Photo by [Alex Grande via Flickr][7]* Websites and apps need to consider a few things when trying to maintain focus. Here are a few steps to help guide a focused design lifecycle: 
1.  What is the primary goal of your application? Keep that in mind as you make design decisions.
2.  Create a styleguide, a one page document with all the colors and styles. Create it in HTML and CSS. To make a styleguide, search and extract patterns from the designs. Things like paragraphs, headers, horizontal rules, and forms all can be included.
3.  Only allow two fonts. I tend to choose one embedded font used for headers and buttons. The other font is a standard typeface, such as Helvetica and Arial, for paragraphs and links.
4.  Keep your colors to a minimum. If you are using Sass, you can create a colors.sass file to save the colors as variables. Choosing colors on future designs will be as easy as scrolling a short list of existing colors. Follow those four rules, your design will be consistent and well-maintained. 

## Tell a story Research shows that if you tell a chronological story to a jury, they are more likely to believe your case. This heuristic is innate in humans and extends to all parts of life. Humans are storytellers and if you tell a good story, people will believe in your product. Storytelling is why vertical parallax sites are so popular. They create evocative storytelling. What's your story? How will it play out on your homepage? 

[<img class="alignnone size-full wp-image-336" src="http://flippinawesome.org/wp-content/uploads/2013/05/parallax.jpg" alt="parallax" width="480" height="249" />][8] 
## Bigger is Better A/B tests indicate that the bigger the action button the better in homepage sign up conversions. If you beef up your headers and buttons, users will unconsciously associate your site with being larger than life. This is true for images as well, A/B tests have shown that full-screen images, when used appropriately, can increase your conversion. 

[<img class="alignnone size-full wp-image-337" src="http://flippinawesome.org/wp-content/uploads/2013/05/treehouse.jpg" alt="treehouse" width="580" height="400" />][9] 
## Whenever possible, use the golden ratio: 1.6 The golden ratio is found throughout nature. Trees, shells, and the Fibonacci Sequence all follow 1.6. You can use this simple ratio to your advantage when choosing header styles or how you layout your website's columns. For example, if your first header (h1) font size is 40px, try 40 / 1.6 = 25px for your header 2 (h2). 

[<img class="alignnone size-full wp-image-338" src="http://flippinawesome.org/wp-content/uploads/2013/05/shell.jpg" alt="shell" width="463" height="362" />][10] 
## Use a grid Grids are commonplace with the advent of responsive web design. Grids allow you to lay out a site using the golden ratio. It helps to set up your layouts following a grid in your styleguide. Grids, golden ratio, and storytelling are all part of our natural environment. Stay in tune with nature and your designs will be focused and simple. 

[<img class="alignnone size-full wp-image-339" src="http://flippinawesome.org/wp-content/uploads/2013/05/nytimes-grid.jpg" alt="nytimes-grid" width="350" height="349" />][11] 
## Write clear messaging, and drop the evocative hype Fancy headlines and witty copywriting may be interesting, but if the user doesn't know what your product does, they won't want to use it. 

1.  Come up with a few clear headlines.
2.  Test them out in person and using Google Adwords.
3.  Go with the slogan that most people understand and also gets the best Click Through Rate (CTR) on Google Adwords.

## When in doubt, follow the leaders If you aren't sure how to proceed with a design, then shamelessly browse around on all the top sites. See what is happening on Behance, Bootstrap, Apple, or 37Signals. See how they solve an autocomplete list, an image carousel, or an user profile. Adapt your styleguide with their suggestions. After a night's sleep revisit what you came up with, and create a better version. Innovation often comes from copying. Copy away! 

## Think about the "F" Users read websites in the shape of an "F". They browse down and run right to scan over content. I should note that sometimes people read in "E" shape, as shown in the image below. 

[Here's an article on that.][12] [<img class="alignnone size-full wp-image-340" src="http://flippinawesome.org/wp-content/uploads/2013/05/f_reading_pattern_eyetracking.jpg" alt="f_reading_pattern_eyetracking" width="785" height="364" />][13] 
## Draw on paper, then in CSS If you are a web developer, you may want to skip the Photoshop step. 

1.  Get out a big piece of blank paper and a pen.
2.  List out all the features you need to design.
3.  Rate the features in importance.
4.  Fit the features in order of importance in a grid or an existing layout.
5.  When the design feels good enough, jump into CSS and build a prototype. If Photoshop is needed, I take a screenshot of the website I've already built and paste it into Photoshop. It allows the designs to come out more realistic and I am able to Photoshop it faster. 

## When really in doubt, hire a contract designer For 

[Recognize][14], we hired a couple of fantastic designers to design our marketing site based off of our business requirements. Once the marketing designs were done, I used those styles in the rest of our site. A couple mockups acted as the design foundation for Recognize. Consider doing the same for your business. If your design isn't at the level of your business requirements, use Dribbble or Behance to find a designer that meets your style needs, and hire them. When you email them, ask if they know anyone else in case they are too busy for new work. Chances are they have a friend that is just as good. 
## Have fun and keep trying That's the most important, if you are having fun you will want to keep trying and if you keep trying you will get better. Simple as that. Expand your mind by learning to design your own sites. 

*This article was originally posted at <http://blog.recognizeapp.com/web-design-tips-for-developers/>*

 [1]: /authors/alex-grande
 [2]: https://www.recognizeapp.com/
 [3]: http://flippinawesome.org/wp-content/uploads/2013/05/Enlightenment_2-1024x819.jpg
 [4]: http://flippinawesome.org/wp-content/uploads/2013/05/iphone-gesture-300x288.jpg
 [5]: http://flippinawesome.org/wp-content/uploads/2013/05/Screen-Shot-2013-04-26-at-2.01.52-PM-1024x762.png
 [6]: http://flippinawesome.org/wp-content/uploads/2013/05/jpeg.jpeg
 [7]: http://www.flickr.com/photos/alexgrande/490918404/in/photostream
 [8]: http://flippinawesome.org/wp-content/uploads/2013/05/parallax.jpg
 [9]: http://flippinawesome.org/wp-content/uploads/2013/05/treehouse.jpg
 [10]: http://flippinawesome.org/wp-content/uploads/2013/05/shell.jpg
 [11]: http://flippinawesome.org/wp-content/uploads/2013/05/nytimes-grid.jpg
 [12]: http://www.nngroup.com/articles/f-shaped-pattern-reading-web-content/
 [13]: http://flippinawesome.org/wp-content/uploads/2013/05/f_reading_pattern_eyetracking.jpg
 [14]: http://www.recognizeapp.com/