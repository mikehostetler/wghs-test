---
ID: 1246
post_title: Express.js Fundamentals
author: Azat Mardan
post_date: 2013-11-11 12:48:59
post_excerpt: ""
layout: post
permalink: >
  http://modern-web.dev/2013/11/11/express-js-fundamentals/
published: true
---
*By [Azat Mardanov][1]* Express.js is an amazing framework for Node.js projects and used in a majority of Node-based web apps. Unfortunately, there's a lack of tutorials and examples on how to write good production-ready code. To help mitigate this need, I released a book called [Express.js Guide: The Most Popular Node.js Framework Manual][2]. However, all things start from the basics, and for this post will give you a taste for the basics of the framework so you can decide if you want to continue the learning it further. 
## Installation Assuming you downloaded and installed Node.js (and npm with it), run this command: 

    $ sudo npm install -g express@3.4.3

## CLI Now we can use command-line interface (CLI) to spawn new Express.js apps: 

<pre>$ express -c styl expressfun
$ cd expressfun && npm install
$ node app</pre> When this is complete, open your browser to 

`http://localhost:3000`. If you'd like to try a simple Express app, below is the full code of `expressfun/app.js` (Note: going forward, as of v3.4.3, bodyParser is deprecated): 
    var express = require('express');
    var routes = require('./routes');
    var user = require('./routes/user');
    var http = require('http');
    var path = require('path');
    
    var app = express();
    
    // all environments
    app.set('port', process.env.PORT || 3000);
    app.set('views', __dirname + '/views');
    app.set('view engine', 'jade');
    app.use(express.favicon());
    app.use(express.logger('dev'));
    app.use(express.bodyParser());
    app.use(express.methodOverride());
    app.use(app.router);
    app.use(express.static(path.join(__dirname, 'public')));
    
    // development only
    if ('development' == app.get('env')) {
      app.use(express.errorHandler());
    }
    
    app.get('/', routes.index);
    app.get('/users', user.list);
    
    http.createServer(app).listen(app.get('port'), function(){
      console.log('Express server listening on port ' + app.get('port'));
    });

## Routes If you open the expressfun/app.js, you'll see two routes in the middle: 

    ...
    app.get('/', routes.index);
    app.get('/users', user.list);
    ... The first one basically takes care of all the requests to the home page (e.g., 

`http://localhost:3000/`) and the latter of requests to `/users`, such as `http://localhost:3000/users`. Both of the routes process URLs case insensitively and in a same manner as with trailing slashes. The request handler itself (`index.js` in this case) is straightforward: every thing from the HTTP request is in `req` and you write results to the response in `res`: 
    exports.list = function(req, res){
      res.send("respond with a resource");
    };

## Middleware Each line above the routes in the code above is a middleware: 

    app.use(express.favicon());
    app.use(express.logger('dev'));
    app.use(express.bodyParser());
    app.use(express.methodOverride());
    app.use(app.router);
    app.use(express.static(path.join(__dirname, 'public'))); Middleware are a pass thru functions that add something useful to the request as it travels along each of them, for example 

`req.body` or `req.cookie`. For more details on middleware, check out my prior post called [Intro to Express.js: Parameters, Error Handling and Other Middleware][3]. 
## Configuration Here is how we define configuration in a typical Express.js app: 

    app.set('port', process.env.PORT || 3000);
    app.set('views', __dirname + '/views');
    app.set('view engine', 'jade'); An ordinary settings involves a name (e.g., views) and a value (e.g., path to the folder where our templates/views live). There is more than one way to define certain settings, for example 

`app.enable` for boolean flags. 
## Jade The Jade template engine is akin to Ruby on Rails' Haml in the way it uses whitespace and indentation. For example, let's look at 

`layout.jade`: 
    doctype 5
    html
      head
        title= title
        link(rel='stylesheet', href='/stylesheets/style.css')
      body
        block content It's possible to utilize full-blown JavaScript code inside of Jade templates. 

## Conslusion Although we've barely scratched the surface, hopefully you can see how easy it is to create MVC web apps with Express.js. The framework works great for creating REST API's as well. If you interested in using express for that purpose, check out my prior tutorials on 

[Tutorial: Node.js and MongoDB JSON REST API server with Mongoskin and Express.js][4] and [Intro to Express.js: Simple REST API app with Monk and MongoDB][5]. If you want to know more about other middlewares and configurations, check out [Express.js API docs][6], [Connect docs][7] and of course my book — [Express.js Guide][2]. For those who already familiar with some basics of Express.js, I recommend going through [ExpressWorks][8], an automated Express.js workshop. *This article was originally published at <http://webapplog.com/express-js-fundamentals/>*

 [1]: /authors/azat-mardanov
 [2]: http://expressjsguide.com/
 [3]: http://flippinawesome.org/2013/05/28/intro-to-express-js-parameters-error-handling-and-other-middleware/
 [4]: http://webapplog.com/tutorial-node-js-and-mongodb-json-rest-api-server-with-mongoskin-and-express-js/
 [5]: http://webapplog.com/intro-to-express-js-simple-rest-api-app-with-monk-and-mongodb/
 [6]: http://expressjs.com/api.html
 [7]: http://www.senchalabs.org/connect/
 [8]: http://webapplog.com/expressworks