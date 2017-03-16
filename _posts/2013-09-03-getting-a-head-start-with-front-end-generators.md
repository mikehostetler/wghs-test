---
ID: 922
post_title: >
  Getting a Head Start with Front End
  Generators
author: admin
post_date: 2013-09-03 11:45:59
post_excerpt: ""
layout: post
permalink: >
  http://modern-web.dev/2013/09/03/getting-a-head-start-with-front-end-generators/
published: true
dsq_thread_id:
  - "2811084104"
om_counter:
  - "107"
---
*By [Jonathan Fielding][1]* In this post we will talk about the different generators we can use to get started on our projects. There are many different generators/scaffolders available, and we will cover a few of the most popular and look at how we can use them in our projects. 
## Initializr ([www.initializr.com][2]) Initializr was one of the first generators for generating a project based upon HTML5 Boilerplate. So if you are a fan of HTML5 Boilerplate then Initializr is a fantastic place to start as it allows you to fine tune to HTML5 Boilerplate to meet your needs. Initially it gives you three pre-configuration options: 

*   Classic H5BP
*   Responsive
*   Bootstrap Initializr then allows you to fine tune your selections by allowing you to select options in the following categories: 

*   *HTML/CSS Template* – Any extra bonuses like Bootstrap or Mobile first responsive you want to add.
*   *HTML5 Polyfill*s – Modernizr or HTML5Shiv
*   *jQuery* – Minified or Development
*   *H5BP Optional* – Allows you to select which parts of H5BP you want to use. To generate your project you simply select the options you want and click download. For a long while Initializr was my port of call when I wanted to start a new project, however in the past year and a half I have moved away from it for one simple reason: the lack of preprocessor support. What Initializr is really missing is the ability to generate projects that use Sass or LESS (or other preprocessors) for generating the CSS and this is a deal breaker for me. However, if you are not using a preprocessor, Initializr could be a good choice. 

## Yeoman ([www.yeoman.io][3]) Yeoman includes a set of tools that allows you to generate the starting points of projects. It allows developers to generate projects directly from the terminal using predefined generators, which you can install. Some of the generators available include: 

*   webapp
*   angular
*   ember
*   backbone
*   chromeapp
*   chrome-extension
*   bootstrap
*   mocha
*   jasmine
*   testacular It is easy to forget however, that Yeoman is more than just a generator. It is also built to be part of a workflow where Yeoman creates your web application, Bower is used to handle dependencies and Grunt is used to preview, test and build. Getting started with Yeoman is pretty easy assuming you have the prerequisites (Node, npm). You simply need to open your terminal and run: 

    npm install yo -g This will install Yeoman for you. This can take some time, so feel free to go grab yourself a tea/coffee/other beverage. Once Yeoman is installed you are ready to create a new project. First things first, you need a directory for our project. Either create one in Finder/Windows Explorer and navigate to it via the Terminal or simply create one directly in the command line. In the case of a Mac I would create the folder by doing the following. 

    cd ~/Sites  mkdir project_directory
    cd project_directory Now that you are in the directory we can get onto generating the project. The first thing you need to do is install the generator; for the first project let's create a simple website. Install the ‘webapp’ generator. To do this we first need to run Yeoman in the terminal: 

    yo You then need to select "Install a generator" using the arrow keys and pressing enter. You will then be prompted to enter a search term; enter "webapp". Yeoman will then give us a list of results, when I did this I got the following results 

    > generator-hazdev-webapp 
        generator-hbswebapp 
        generator-mobile 
        generator-sails 
        generator-webapp 
        generator-webapp-bfytw 
        generator-webapp-fintan 
        Search again 
        Return home You then need to select 

`generator-webapp` and hit enter. This will download and install the webapp generator for you. You should now be presented with the original list with an added option to "Run the Webapp generator". Select this option and hit enter. You will be presented with the chance to select what you need for the project. To select options use the arrow keys and use the space bar to select/deselect as needed. 
    Out of the box I include HTML5 Boilerplate and jQuery.
    [?] What more would you like? 
     ❯⬢ Twitter Bootstrap for Sass
      ⬢ RequireJS
      ⬢ Modernizr Once you have selected what you need, Yeoman will start downloading everything for your project.  This can take some time depending on your Internet connection, so if you have finished your first beverage it might be time to get another. So once Yeoman is finished you will get a lovely "Bye from us! Chat soon." message from the authors of Yeoman. So at this point you have a generated project with a Gruntfile preconfigured with the following commands: 

*   `grunt` – runs jshint on the JavaScript followed by the `test` and `build` commands.
*   `grunt build` – compiles the projects, including minifying/concatenating .js files, builds the Sass into minified CSS and compresses and renames images (for cache busting).
*   `grunt watch` – watches the JavaScript, CoffeeScript and Sass to detect any changes, and recompiles them if they change.
*   `grunt server` – creates a node server for running the project, watches for any file changes and refreshes the browser on a file change.
*   `grunt test` – runs any tests setup for the project.

### Summary of Yeoman Yeoman is a fantastic tool that I use a lot for generating projects. It is fantastic for getting started when I start a new project. Prior to Yeoman I kept my own template in git which I used time and time again for new projects. However, this was hard work as I had to regularly update the libaries in the template. Yeoman allows me to start a project with the most up to date libraries, which saves time from having to maintain my own template. 

## grunt-init (<http://gruntjs.com/project-scaffolding>) grunt-init is another command line tool for generating projects, similarly to Yeoman (which, as we mentioned, includes Grunt). To install use: 

    npm install -g grunt-init Once this is installed you then download your first template. The best way to install a grunt-init template is to clone it from GitHub. 

    git clone git@github.com:pascalduez/grunt-init-webapp.git ~/.grunt-init/webapp Now that you have set up our environment, you need a directory for your project. Either create on in Finder/Windows Explorer and navigate to it in the Terminal or simply create one in terminal. In the case of a Mac I would create the folder by doing the following: 

    cd ~/Sites  mkdir project_directory  cd project_directory You now can generate your web app using: 

    grunt-init webapp You will then be asked a series of questions: 

    Please answer the following:
    [?] WebApp package and root directory. (project_directory) 
    [?] WebApp name. (A human-readable name for the app: Project Directory) 
    [?] WebApp description. (A human-readable description of the app.) 
    [?] Version (0.1.0) 
    [?] Project git repository (git://github.com/jonathan/project_directory.git) 
    [?] Project homepage (https://github.com/jonathan/project_directory) 
    [?] Author name (Jonathan Fielding) 
    [?] Author url (none) 
    [?] Licenses (MIT) 
    [?] Do you need to make any changes to the above before continuing? (y/N) Once you have answered all the questions grunt-init will download and install the required components. So at this point you have a generated project with a Gruntfile preconfigured with the following commands: 

*   `grunt` – runs jshint on the JavaScript.
*   `grunt server` – creates a Node server for running the project, watches for any file changes and refreshes the browser on a file change.

### Summary of grunt-init With grunt-init we can generate a project based on templates we clone from Git, but this is part of what makes grunt-init not as useful vs Yeoman. Yeoman takes away the worry about ensuring our templates are up to date. What grunt-init really needs is some sort of repository that works similarly to Bower, pulling the templates from GitHub. 

## Summary of Generators So it is very difficult to compare the three different generators because they are all very different, Initializr is a fantastic tool for generating a project quickly. However, if you want more fine grained control you should look at using a command line generator like Yeoman or grunt-init. When comparing both Yeoman and grunt-init it becomes harder. They both work in different ways and may each suit  a persons personal workflow better. The key thing that makes me use Yeoman versus using grunt-init is that Yeoman makes it easier to install generators and to ensure that the generators are up to date. 

*This article was originally published at [http://www.jonathanfielding.com/frontend-tooling-generators][4]*

 [1]: /authors/jonathan-fielding
 [2]: http://www.initializr.com/
 [3]: http://www.yeoman.io/
 [4]: http://www.jonathanfielding.com/frontend-tooling-generators/