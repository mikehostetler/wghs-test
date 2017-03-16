---
ID: 400
post_title: >
  Intro to Express.js Parameters, Error
  Handling and Other Middleware
author: admin
post_date: 2013-05-28 09:53:27
post_excerpt: ""
layout: post
permalink: >
  http://modern-web.dev/2013/05/28/intro-to-express-js-parameters-error-handling-and-other-middleware/
published: true
dsq_thread_id:
  - "2811675966"
sidebar_value:
  - Default Sidebar
sidebar_position_value:
  - right
om_counter:
  - "172"
---
*By [Azat Mardanov][1]* Express.js is one of the most popular and mature Node.js frameworks. In this post, we’ll cover some core Express features in the form of middleware. You can read more about in [Intro to Express.js][2] series on my blog [webapplog.com][3]. To learn how to create an Express application from scratch please refer to [the earlier post][4]. 
## Request Handlers Express.js is a Node.js framework that among other things provides a way to organize routes. Each route is defined via a method call on an application object with a URL pattern as a first parameter (RegExp is also supported), for example: 

    app.get('api/v1/stories/', function(res, req){
      ...
    }) or, for a POST method: 

    app.post('/api/v1/stories', function(req,res){
      ...
    }) Needless to say, the DELETE and PUT methods are 

[supported as well][5]. The callbacks that we pass to the get() or post() methods are called request handlers. They take requests (req), process them and write to response (res) objects. For example: 
    app.get('/about', function(req,res){
      res.send('About Us: ...');
    }); We can have multiple request handlers, often referred to as middleware. They accept a third parameter, next, which when called (via next()) will switch the execution flow to the next handler: 

    app.get('/api/v1/stories/:id', function(req,res, next) {
      //do authorization
      //if not authorized or there is an error 
      // return next(error);
      //if authorized and no errors
      return next();
    }), function(req,res, next) {
      //extract id and fetch the object from the database
      //assuming no errors, save story in the request object
      req.story = story;
      return next();
    }), function(req,res) {
      //output the result of the database search
      res.send(res.story);
    }); In the above example, the ID of a story in the URL pattern (":id") is a query string parameter that we will use to find matching items in the database. 

## Parameters Middleware Parameters are values passed in the query string of a URL in the request. If we didn't have Express.js or a similar library and had to use just the core Node.js modules, we would need to extract parameters from 

[HTTP.request][6] object via a combination of the require('querystring').parse(url) or require('url').parse(url, true) functions. Thanks to the [Connect framework][7] and people at [VisionMedia][8], Express.js makes it easy to support parameters, error handling and many other important features in the form of middleware. This is how we can plug the param middleware into our app: 
    app.param('id', function(req,res, next, id){
      //do something with id
      //store id or other info in req object
      //call next when done
      next();
    });
    
    app.get('/api/v1/stories/:id',function(req,res){
      //param middleware will be execute before and
      //we expect req object already have needed info
      //output something
      res.send(data);
    });
    For example:
    app.param('id', function(req,res, next, id){
      req.db.get('stories').findOne({_id:id}, function (e, story){
        if (e) return next(e);
        if (!story) return next(new Error('Nothing is found'));
        req.story = story;
        next();
      });
    });
    
    app.get('/api/v1/stories/:id',function(req,res){
      res.send(req.story);
    }); We could also use multiple request handlers but the concept remains the same: we can expect to have req.story object or an error thrown prior to the execution of this code so we abstract the common code/logic of getting parameters and their respective objects: 

    app.get('/api/v1/stories/:id', function(req,res, next) {
      //do authorization
      }),
      //we have an object in req.story so no work is needed here
      function(req,res) {
      //output the result of the database search
      res.send(story);
    }); Authorization and input sanitation are also good candidates for placing in middleware. We can also combine multiple parameters: 

    app.get('/api/v1/stories/:storyId/elements/:elementId',function(req,res){
      res.send(req.element);
    });

## Error Handling Error handling is typically used across the whole application, therefore it's best to implement it as a middleware. It has the same parameters plus one more, error: 

    app.use(function(err, req, res, next) {
      //do logging and user-friendly error message display
      res.send(500);
    }) In fact, the response can be anything: 

*JSON string* 
    app.use(function(err, req, res, next) {
      //do logging and user-friendly error message display
      res.send(500, {status:500, message: 'internal error', type:'internal'});
    })

*Text message* 
    app.use(function(err, req, res, next) {
      //do logging and user-friendly error message display
      res.send(500, 'internal server error');
    })

*Error page* 
    app.use(function(err, req, res, next) {
      //do logging and user-friendly error message display
      //assuming that template engine is plugged in
      res.render('500');
    })

*Redirect to error page* 
    app.use(function(err, req, res, next) {
      //do logging and user-friendly error message display
      res.redirect('/public/500.html');
    })

*Error HTTP response status (401, 400, 500, etc.)* 
    app.use(function(err, req, res, next) {
      //do logging and user-friendly error message display
      res.end(500);
    }) In addition, logging should also be abstracted as middleware. To trigger an error from within your request handlers and middleware you can just call: 

    next(error); or 

    next(new Error('Something went wrong')); You can also have multiple error handlers and use named instead of anonymous functions as shown in the 

[Express.js Error handling guide][9]. 
## Other Middleware The res.json() function conveniently serializes JavaScript/Node.js objects as a JSON string. For example: 

    app.get('/api/v1/stories/:id', function(req,res){
      res.json(req.story);
    }); which is equivalent to (assuming req.story is an Array and Object): 

    app.get('/api/v1/stories/:id', function(req,res){
      res.send(req.story);
    }); or 

    app.get('api/v1/stories/:id',function(req,res){
      res.set({
        'Content-Type': 'application/json'
      });
      res.send(req.story);
    });

## Abstraction Middleware is flexible. You can use anonymous or named functions, but the best thing is to abstract request handlers into external modules based on the functionality: 

    var stories = require.('./routes/stories');
    var elements = require.('./routes/elements');
    var users = require.('./routes/users');
    ...
    app.get('/stories/,stories.find);
    app.get('/stories/:storyId/elements/:elementId', elements.find);
    app.put('/users/:userId',users.update);
    routes/stories.js:
    module.exports.find = function(req,res, next) {
    };
    routes/elements.js:
    module.exports.find = function(req,res,next){
    };
    routes/users.js:
    module.exports.update = function(req,res,next){
    };
    You can use some functional programming tricks, like this:
    function requiredParamHandler(param){
      //do something with a param, e.g., check that it's present in a query string
      return function (req,res, next) {
        //use param, e.g., if token is valid proceed with next();
        next();
      });
    }
    
    app.get('/api/v1/stories/:id', requiredParamHandler('token'), story.show);
    
    var story  = {
      show: function (req, res, next) {
        //do some logic, e.g., restrict fields to output
        return res.send();
      }
    }

## Conclusion As you can see middleware is a powerful concept for keeping code organized. The best practice is to keep your router lean and thin by moving all of the logic into corresponding external modules/files. This way the important server configuration parameters will be neatly in one place, right there when you need them. 

*This post was originally published at <http://www.webapplog.com/intro-to-express-js-parameters-error-handling-and-other-middleware/>*

 [1]: /authors/azat-mardanov
 [2]: http://www.webapplog.com/tag/intro-to-express-js/
 [3]: http://webapplog.com/
 [4]: http://www.webapplog.com/intro-to-express-js-simple-rest-api-app-with-monk-and-mongodb/
 [5]: http://expressjs.com/api.html#app.VERB
 [6]: http://nodejs.org/api/http.html#http_http_request_options_callback
 [7]: http://www.senchalabs.org/connect/
 [8]: https://github.com/visionmedia/express
 [9]: http://expressjs.com/guide.html#error-handling