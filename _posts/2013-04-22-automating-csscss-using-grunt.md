---
ID: 216
post_title: Automating CSSCSS using Grunt
author: admin
post_date: 2013-04-22 12:18:25
post_excerpt: ""
layout: post
permalink: >
  http://modern-web.dev/2013/04/22/automating-csscss-using-grunt/
published: true
dsq_thread_id:
  - "2809873726"
om_counter:
  - "36"
---
*By [Peter Keating][1]* Recently a really useful tool [Zach Moazeni][2] named [CSSCSS][3] received a great deal of attention. CSSCSS is a Ruby gem that parse CSS files and will detect selectors with duplicated rules. This tool is great for helping you reduce the size and complexity of stylesheets by removing duplication. This can help in shaving off those crucial kilobytes from your CSS files but can also prevent unexpected results caused by duplicate rules. A great example use case for CSSCSS is when working with unfamiliar stylesheet. CSSCSS can be used to ensure you're not adding additional CSS that has already been defined somewhere else in the stylesheet. CSSCSS has lots of other little features such as the ability to convert and analyze [Sass][4] or [LESS][5] files, output analysis in JSON, ignore dictated properties or selectors and change the minimum number of matched rules before a ruleset is considered matched. 
## Getting Started with CSSCSS To paint a clear picture of what CSSCSS actually does, lets run through a simple example. Below is some CSS that will be analyzed. Obviously, this example is intentionally simple and therefore the matched rules are easy to see, but imagine these are part of a much larger stylesheet. 

    .rule-a {
        border: 1px solid #000;
        margin: 0;
        padding: 0;
    }
    
    .rule-b {
        border: 1px solid #000;
        margin: 0;
        padding: 0;
    } First, let's install CSSCSS via the command line using: 

    gem install csscss The gem command can only be run if you have 

[Ruby installed][6], and only needs to be run the first time before you can use CSSCSS. Next we'll run CSSCSS on our above stylesheet, which I have saved as style.css. To run the test, you can use the following command (assuming style.css is in your current directory): 
    csscss -v style.css The -v argument instructs CSSCSS to output the properties that have been matched. As you can see CSSCSS has outputted that .rule-a and .rule-b are sharing rules. This signals that a refactor of the CSS should be done so that only one rule exists for the matched properties. 

[<img class="alignnone size-full wp-image-217" alt="csscss-screenshot" src="http://flippinawesome.org/wp-content/uploads/2013/04/csscss-screenshot.png" width="600" height="312" />][7] 
## Using CSSCSS with Grunt Now you could execute CSSCSS manually while working on CSS. However it might be especially useful if CSSCSS were included as part of the automation process. Over the past month I have spent a lot of time getting to know 

[Grunt][8], which is a perfect solution for just this kind of automation. Grunt is a JavaScript task runner used to configure an automation process that runs on [Node.js][9]. While [Ant][10] has in the past been my tool of choice for automation, using Grunt makes it especially simple to configure automation tasks for modern web frameworks and libraries. The Grunt configuration is defined in a JavaScript file that is placed inside your project. This JavaScript file contains tasks that are configured to your project's automation needs. Often these tasks utilize Grunt's long list of official and community created [plugins][11] which can be installed via [NPM][12]. To find out more about Grunt and how it can be configured I suggest reading the well-written [documentation][13]. As CSSCSS is relatively new, a Grunt task didn't yet exist for it. I saw an opportunity to make an open source contribution as well as a chance to learn more about Grunt and Node.js by creating and sharing a Grunt task that would execute CSSCSS. The Grunt task, which is named [grunt-csscss][14], is published on [NPM][15]. The source code and documentation is available on [Github][14]. So how do you use it? Firstly, of course, you need to have Node.js installed on your machine. Next you need to install the [Grunt command line tool][13] as well as the actual [Grunt package][16]. You'll also need to have a package.json file containing your dependencies as well as a [Gruntfile.js][17] file that will contain your automation tasks. If you haven't installed or used Grunt before, be sure to check out the [getting started guide][13] for more detailed instructions. Once you have all these you can run the command below to install grunt-csscss as a development dependency. 
    npm install grunt-csscss --save-dev Using the --save-dev command will add grunt-csscss to the package.json dependencies for this project automatically. Let's look at how to create a task using this plugin within our Gruntfile. Below is an example of a Gruntfile that uses grunt-csscss to analyze style.css with CSSCSS in verbose mode. Multiple files can be analyzed individually by specifying them within the src array. There are many more 

[options][18] available to change the arguments that are passed to CSSCSS. 
    'use strict';
    
    module.exports = function(grunt) {
    
      grunt.initConfig({
        csscss: {
          options: {
            verbose: true
          },
          dist: {
            src: ['style.css']
          }
        }
      });
    
      grunt.loadNpmTasks('grunt-csscss');
    
      grunt.registerTask('default', ['csscss']);
    }; To run this task individually you can write the following via the command line: 

    grunt csscss However, since it was added to our default grunt task we can also just use the grunt command. There are 

[more examples][19] of using the csscss task in the documentation. 
## Where to go from here All this is ready to be used right now. Currently the Grunt plugin supports all the arguments available in the current version of CSSCSS. I will be working to ensure that it continues to support all the features that are added to CSSCSS in the future. Feel free to give it a try, and any feedback or contributions are appreciated. You can send feedback by 

[contacting me][20] or [opening an issue][21] in the Github repository. One of the primary things I learnt from creating grunt-csscss was how easy things have been made for developers with tools like Grunt, NPM and [Travis CI][22]. If you're developing using modern tools, frameworks & libraries like Sass, [RequireJS][23] or JSHint to name a few, then you should definitely have Grunt and Node in your arsenal. Grunt is definitely a game changer for automation and I will definitely be using it as my primary automation tool over [Ant][10]. Finally, massive credit is due to Zach for his work creating CSSCSS! *This article was originally published at <http://peterkeating.co.uk/using-csscss-with-grunt/>* *Image courtesy of <http://commons.wikimedia.org/wiki/File:Harmony_circles.png>*

 [1]: /authors/peter-keating
 [2]: http://connectionrequired.com/
 [3]: http://zmoazeni.github.io/csscss/
 [4]: http://sass-lang.com/
 [5]: http://lesscss.org/
 [6]: http://www.ruby-lang.org/en/downloads/
 [7]: http://flippinawesome.org/wp-content/uploads/2013/04/csscss-screenshot.png
 [8]: http://gruntjs.com/
 [9]: http://nodejs.org/
 [10]: http://ant.apache.org/
 [11]: http://gruntjs.com/plugins
 [12]: https://npmjs.org/
 [13]: http://gruntjs.com/getting-started
 [14]: https://github.com/peterkeating/grunt-csscss
 [15]: https://npmjs.org/package/grunt-csscss
 [16]: https://npmjs.org/package/grunt
 [17]: http://gruntjs.com/sample-gruntfile
 [18]: https://github.com/peterkeating/grunt-csscss#options
 [19]: https://github.com/peterkeating/grunt-csscss#examples
 [20]: http://peterkeating.co.uk/about/
 [21]: https://github.com/peterkeating/grunt-csscss/issues
 [22]: https://travis-ci.org/
 [23]: http://requirejs.org/