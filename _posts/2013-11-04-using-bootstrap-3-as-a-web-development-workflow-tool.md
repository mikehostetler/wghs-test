---
ID: 1213
post_title: >
  Using Bootstrap 3 as a Web Development
  Workflow Tool
author: admin
post_date: 2013-11-04 12:12:01
post_excerpt: ""
layout: post
permalink: >
  http://modern-web.dev/2013/11/04/using-bootstrap-3-as-a-web-development-workflow-tool/
published: true
dsq_thread_id:
  - "2809885325"
om_counter:
  - "452"
---
*By [Zing Design][1]* With the release of Bootstrap 3 in mid-August, I was eager to take it out for a test drive. My expectations were pretty high for the latest iteration of the framework. The first thing that struck me when I was reading through the documentation were the many similarities to [Foundation 4][2]. At first I was all like, "Hey, the Bootstrap guys have finally caught up with Zurb, that's nice...", but after looking at Bootstrap 3 some more, I realized just how much further they'd taken things. They've created a whole lot more than just a springboard for building slick start-up style websites, they've designed an entire development workflow. This new environment allows developers to quickly put together a flat prototype with reusable templates and partials written in their favorite templating language. Similar to [Yeoman][3], Bootstrap 3 uses Bower and Grunt to do the manual labor. It also includes Jekyll as a builder and server, [Liquid][4] for tidier templates and [Recess][5] for Less pre-processing. I was really impressed with the new setup, and so eager to get into the new workflow I decided to put down Foundation and give Bootstrap 3 a try for our latest project. 
## Getting Set Up Setting up a new Bootstrap is now even easier! For development, I recommend installing straight from Bower (as suggested on getbootstrap.com). If you haven't started using Bower already, give it a go. It saves a whole heap of time and headaches fetching the latest version of all your favorite front-end packages. Bower is dependent on 

[Git][6] and [Node.js][7], so you'll need to install those first if you haven't already. To install Bower: 
    $ [sudo] npm install -g bower Then install a Bootstrap: 

    $ cd ~/path/to/working/folder/
    $ [sudo] bower install bootstrap You'll notice some big changes in the folder structure, with the most noticeable being that Bootstrap 3 is actually structured like a web app. This is great, as it defines a consistent folder tree, making it easier to integrate into existing solutions and forcing devs into good habits. 

## Take it out for a spin Like a kid bringing home a new transformer, the first thing you'll want to do is take Bootstrap out of the metaphorical box and play with it in the browser. There's just a couple of tasks to carry out first. 

### Task running with Grunt If you haven't come across Grunt, here's a great opportunity to become friends with it. Grunt is a wonder-tool which deals with all the nitty-gritty tasks that can make front-end development rather tedious. It can pretty much automate any task you can possibly imagine. As an example, some of the most useful tasks included in the Bootstrap 3 install are JS hinting, concatenation and uglifying, Less preprocessing (via Recess) and HTML validation. You'll need to use npm to install all the necessary modules with 

`npm install`, then run the Grunt default task and watch as it does a whole bunch of cool stuff in the terminal. 
    $ cd app/components/bootstrap
    $ [sudo] npm install
    $ grunt default

** You can leave off the ‘default' in ‘grunt default', this is simply to illustrate how one would run any given Grunt task.* You'll notice a lot has changed in your folder structure, Grunt, in all it's wonderful magicalness, has just created a website by combining the power of all those fancy tools we discussed previously. There's just one more step and we can try it out in the browser... 
#### Simple serving with Jekyll

<a title="Jekyll - check it out" href="http://jekyllrb.com/" target="_blank">Jekyll</a> is a Ruby-based tool for setting up a semi-dynamic blog-style site in minutes. No more mucking about installing and/or configuring WAMP, XAMPP, LAMP or MAMP and no database skulduggery. We just want to test out our static little Bootstrap site. On Mac, Jekyll is dependent on <a title="Find out how to install Ruby here" href="https://www.ruby-lang.org/en/downloads/" target="_blank">Ruby</a> and <a title="Install Rubygems too" href="http://rubygems.org/pages/download" target="_blank">RubyGems</a>, you should be able to get those two installed in minutes. On Windows, getting Jekyll isn't quite so straightforward. The Jekyll website pointed me at this <a title="Set up Jekyll on Windows... It'll only take about half an hour :(" href="http://www.madhur.co.in/blog/2013/07/20/buildportablejekyll.html" target="_blank">tutorial for setting up Jekyll on Windows</a>. Once you've got that all sorted, install Jekyll… 
    $ [sudo] gem install jekyll Start Jekyll up in a new window with: 

    $ jekyll serve Now if all that worked, we should be able to navigate to 

`localhost:9001/` and see the familiar getbootstrap site which the Bootstrap 3 guys decided would make a good template to start off with. If it seemed like a lot of work to do something that would've taken seconds to do with Bootstrap 2, well it is - but it's well worth it. From here you can go in two directions: take the site we've just generated (in the \_gh\_pages folder) and use it as is and never to speak of all these awesome tools again *or* carry on using Bootstrap as part of your new badass web development workflow. If you're worried you'll have to go through this process every time you want to test some changes, never fear. You can ask all of these tools to keep watching your code for changes with a few commands. Watch for changes to the JS and Less source files in Grunt 
    $ grunt watch Start up the server again with the watch option to watch for changes and regenerate automatically 

    $ jekyll serve -w

*Note on Jekyll: One of the limitations of Jekyll is that it doesn't support POST requests, just in case you were thinking of getting carried away and building your whole app with it. Bootstrap's development environment is really just for whipping up a prototype, so you will eventually need to switch to a more meatier testing server.* 
### Watching custom Javascript You may want to extend Bootstrap further with your own fancy JavaScript. You'll also probably want to add the same automation as is used on the included files. First, add your new script to the "js" folder. Next, you'll need to add the script name to the 

`concat` task: 
    concat: {
      options: {
        banner: '<%= banner %><%= jqueryCheck %>',
        stripBanners: false
      },
      bootstrap: {
        src: [
          'js/transition.js',
          // ... Other Bootstrap scripts ...
          //New script name goes here ...
          'js/my-awesome-script.js' //Example
        ],
        dest: 'dist/js/<%= pkg.name %>.js'
      }
    } To automatically watch scripts, add these two objects to the 

`watch` task: 
    js: {
      files: 'js/*.js',
      tasks: ['concat', 'uglify']
    },
    jekyll: {
      files: 'js/*.js',
      tasks: ['jekyll']
    } You now have a fully automated development environment where scripts are automatically hinted, combined and compressed, Less is hinted and compiled on the fly and HTML is validated. The days of slaving over these crucial optimization tasks are over. Let's see how we can take it even further. 

### Image optimization One of the other tasks which I've found to be repetitive and tedious in the past is image optimization through compression. There are various tools available, web tools like 

[tinypng.org][8] and desktop tools like [ImageOptim][9] for Mac. However, I found a Grunt module called imagemin which fills this role pretty well. Here's how I set it up with Bootstrap 3. Start by installing imagemin with npm: 
    $ [sudo] npm install grunt-contrib-imagemin --save-dev Next we'll make some folders to keep our uncompressed and compressed images in. 

*Note: These can be made anywhere (and you can call them whatever you like), I've put them in the "assets" folder.* 
    $ cd app/components/bootstrap/assets
    $ mkdir images images/uncompressed In the Gruntfile.js 

`initConfig` object: 
    // bootstrap/Gruntfile.js 
    // ...
    grunt.initConfig ({
      //other git config rules go here ...
      imagemin: {
        dynamic: {                         
          files: [{
            expand: true,                  
            cwd: 'assets/images/uncompressed',                   
            src: ['**/*.{png,jpg,gif}'],   
            dest: 'assets/images/'                  
          }]
        }
      }
    }); Register the task with Grunt: 

    // bootstrap/Gruntfile.js ...
    grunt.loadNpmTasks('grunt-contrib-imagemin'); Define a custom task to run manually: 

    grunt.registerTask('images', ['imagemin']); Or automatically watch for new images: 

    // bootstrap/Gruntfile.js
    grunt.initConfig ({
      // ...
      watch {
        // other watch tasks
        // ... ,
        imagemin: {
          files: 'assets/images/uncompressed/*.{png,jpg,gif}',
          tasks: ['imagemin']
        }
      }
    }
    }); Lastly you'll probably want to tell Git to ignore the "assets/images/uncompressed" folder in 

`bootstrap/.gitignore` and you're good to go. Just restart `grunt watch` and it'll check your uncompressed folder for new images, then compress them as needed. 
## Bootstrap with Compass? Foundation fan-boys are probably tempted to give up and go back over to F4 where they can use Sass. Before you do, have a look at this. Aaron Lademann has created a pretty decent fork of Bootstrap called 

<a title="Sass Bootstrap by Aaron Lademann" href="http://alademann.github.io/sass-bootstrap/" target="_blank">Sass Bootstrap</a>. This, as you may have guessed, is Bootstrap 3 with all the Less files painstakingly converted to Sassy CSS. You can install it the same way we installed Twitter's Bootstrap: 
    $ cd ~/path/to/my/favourite/folder
    $ bower install alademann-sass-bootstrap
    $ npm install
    $ grunt Alternatively, if you don't want to go through that process again, you can just set up Compass in Bootstrap's assets folder and run your custom stylesheets separately to Bootstrap. 

    $ gem install compass 
    $ cd ~/path/to/bootstrap/assets 
    $ compass create --javascripts-dir "js" Then you can customize Bootstrap 3 using the superior CSS pre-processor, which basically solves one of the biggest reasons not to use Bootstrap. 

## Found any other tools to streamline your development process? I'm always on the look-out for the latest in cutting edge development tools. I'd love to hear about your favorites in the comments. I hope you've enjoyed reading this article. If you are interested, my employer, Zing design has a 

[newsletter][10] where you can receive regular updates on our latest projects, research, and other news in the world of web design and development. *This article was oriinally published at <http://www.zingdesign.com/bootstrap-3-as-a-web-development-workflow-tool/>*

 [1]: /authors/zing-design
 [2]: http://www.zingdesign.com/5-reasons-why-foundation-is-better-than-twitter-bootstrap/ "5 reasons why Foundation is better than Twitter Bootstrap 2"
 [3]: http://yeoman.io/ "Yeoman: Modern workflows for modern web apps"
 [4]: http://wiki.shopify.com/Liquid "Shopify's Liquid syntax for tidy templating"
 [5]: https://github.com/twitter/recess "Twitter's Recess, for hinting and compiling Less stylesheets"
 [6]: http://git-scm.com/book/en/Getting-Started-Installing-Git "Install Git now!"
 [7]: http://nodejs.org/ "Install Node.js as well. It will change your life"
 [8]: http://tinypng.org/ "Tiny PNG: the go-to web tool for quick lossless PNG compression"
 [9]: http://imageoptim.com/ "A Mac desktop app for bulk image compression"
 [10]: http://www.zingdesign.com/subscribe-to-newsletter/ "Subscribe to newsletter"