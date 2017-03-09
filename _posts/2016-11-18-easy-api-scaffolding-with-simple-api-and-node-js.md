---
ID: 67
post_title: >
  Easy API Scaffolding with Simple-API and
  Node.js
author: admin
post_date: 2016-11-18 17:09:03
post_excerpt: ""
layout: post
permalink: >
  http://modern-web.dev/2016/11/18/easy-api-scaffolding-with-simple-api-and-node-js/
published: true
featured_author:
  - 'No'
  - 'No'
---
<em>By <a href="/authors/joseph-wegner">Joseph Wegner</a></em>

API's are often a pain to build. There's all kinds of moving pieces: routing, data parsing, serialization, database interactions, etc. For years I've struggled with writing web API's in Node.js - there's just so much complexity required in an API boilerplate. That's why I wrote <a href="https://github.com/josephwegner/simple-api">Simple API</a>. Simple API is an incredibly easy to use module for writing web-based APIs in Node.js

I often hear people say that they've written a library that makes something "simple", but in reality they've just made it a different kind of complex. In this article, I hope to prove that's not the case with Simple API. This tutorial is going to teach you to write an <a href="https://github.com/josephwegner/simple-example">API for a ToDo list</a>, where the entire project is less than 300 lines of code. That's the entire project - the API related files are less than 200 lines of code.

Note: <a href="https://github.com/josephwegner/simple-example">The examples</a> will all be written in <a href="http://coffeescript.org/">CoffeeScript</a>, but can easily be compiled to Javascript.
<h2><a href="https://gist.github.com/josephwegner/6588788#setup" name="setup"> </a>Setup</h2>
Of course, every module requires a little bit of time to install and setup the boilerplate. That's where we'll start. First off, you need to install Simple API. This is very easy using <a href="http://npmjs.org/">NPM</a>.
<pre><code class="language-javascript">npm install simple-api</code></pre>
See, that was simple! Now we need to include the module and run some simple setup code. Put the code below into your index.coffee file.
<pre><code class="language-javascript">#Load the Simple API Module
api = require 'simple-api'

#Create API Server
v0 = new api
    prefix: ["api", "v0"]
    host: "localhost"
    port: "3333"
    logLevel: 0</code></pre>
Pretty simple as well, but let's talk through each of those lines.

<codeclass="language-javascript" >api = require 'simple-api'</code>: This loads the Simple API module. The api object here is a class waiting to be instantiated.

<code class="language-javascript">v0 = new api</code>: This instantiates the API object. I'm calling this API 'v0', so I named the variable that. Everything after this line is in the config object for the API.

<code class="language-javascript">prefix: ["api", "v0"]</code>: This is the path that will prefix all API calls. Each element in the array is a part of the URL. This would match "http://host/api/v0". You can also use a string to configure the prefix, like "api/v0".

<code class="language-javascript">host: "localhost"</code>:This is the host that the API should be listening on. If you want to listen to all hosts, set this to null

<code class="language-javascript">port: 3333</code>: This is the port that the API will be listening on. If you want to listen on port 80, set this to null.

<code class="language-javascript">logLevel: 0</code>: Simple API has 5 log levels. A lower number means less logs.

There you go! If you run <code class="language-javascript">coffee index</code> you will see the server startup, and Simple API will output a log that it's listening for requests.
<h2><a href="https://gist.github.com/josephwegner/6588788#controllers" name="controllers"> </a>Controllers</h2>
Unfortunately, as you'll notice, your API doesn't actually do anything at this point. If you make any requests to it you will get a <code>404 Not Found</code> response. That's because you don't have any Controllers. Controllers are a pretty broad way to represent any object that your API can talk about. You might have a Controller for a user, a task, a tweet - any "Object". For the ToDo app, we will have a single controller for tasks.

I prefer to organize my API by creating <a href="https://github.com/josephwegner/simple-example/tree/master/api">an <code>api</code> folder</a>, with a <code>version</code> folder, and then place <code>controllers</code> and <code>models</code> folders inside of that. So, in this example, my controllers can be found in <code>./api/v0/controllers</code>.

Controllers have <a href="https://github.com/josephwegner/simple-api#controllers">three different things</a> that need to be configured. <strong>Routes</strong> tell Simple API which URLs are related to the controller. Routes also tell Simple API which part of each URL should be parsed as a parameter. <strong>Actions</strong> are the meat of the controller - they decide what happens for each API request. <strong>Helpers</strong> are functions that may need to be called from multiple actions. There's also an <strong>options</strong> key, but for now it is not used for anything (but are accessable from actions, if you want them).
<h3>Routes</h3>
I like to start my controllers by writing the routes. This allows me to think in an abstract way about how I want people to interact with my API, rather than getting distracted by the technical implementation for each action. First, though, we need to write the overall structure of a controller definition:
<pre><code>TasksController =
    options: {} #This doesn't get used
    routes: {}
    actions: {}
    helpers:  {}</code></pre>
Now we can get started writing the routes. It's important to name your routes descriptively, because they will need to match the name of your actions. When you come back to maintain your code, the name of the routes and actions are the easiest way to find the section you're looking for.
<pre><code>TasksController =
    options: {}
    routes:
        getAllTasks: 
            method: "GET"
            path: []</code></pre>
This is the simplest type of route. It uses the GET method, and has no path. That means it will respond from <code>/api/v0/tasks</code>. It is named <code>getAllTasks</code>, so when an API call matches this route it will call the <code>getAllTasks</code> action.

You'll notice that I'm using an array for the path definition here, similar to the API prefix definition. If you prefer, you can use a string instead.
<pre>getTask:
    method: "GET"
    path: ["*identifier"]</pre>
The <code>getTask</code> route is similar to <code>getAllTasks</code>, but adds a little spice. This route will allow the user to request a single task by ID. You can see that I've defined a single piece to the path: <code>*identifier</code>. The <code>*</code> tells Simple API to match any alphanumeric string for that portion of the URL. There are other match types for alpha-only, numeric-only, or RegExp matching. You can read about those <a href="https://github.com/josephwegner/simple-api#getting-parameters-from-the-url">in the docs</a>.

Whatever matches for this first portion of the URL will be stored in a variable called <code>identifier</code>. You could name that variable anything, but <code>identifier</code> makes the most sense for this example.
<pre>getCategoryTasks:
    method: "GET"
    path: ["category", "*category"]</pre>
<code>getCategoryTasks</code> again builds upon the previous route. This route has a static string match of <code>category</code> in the front, and then another alphanumeric parameter match on the end. That means it will match URLs like <code>http://host/api/v0/category/4lph4Num3r1C</code>. The matched string in the second part of the URL will be stored in the <code>category</code> variable.
<pre><code>createTask: 
    method: "POST"
    path: []</code></pre>
The <code>createTask</code> route looks pretty basic, but it has one important change. The HTTP method is now <code>POST</code>. You can define any HTTP method you want with Simple API, but there are four <a href="http://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html">commonly used methods</a>: <strong>POST</strong> is for <strong>C</strong>reating data, <strong>GET</strong> is for <strong>R</strong>eading data, <strong>PUT</strong> is for <strong>U</strong>pdating data, and <strong>DELETE</strong> is for <strong>D</strong>eleting data. These can easily be remembered as <strong>CRUD</strong>. The rest of the routes will build upon the ideas that you already know.
<pre><code>updateTask:
    method: "PUT"
    path: ["*identifier"]

deleteTask:
    method: "DELETE"
    path: ["*identifier"]

completeTask:
    method: "PUT"
    path: ["*identifier", "complete"]</code></pre>
<h3>Actions</h3>
Great! Now we've got all of our routes defined, and can move on to the actual action code. As I've mentioned, the names of your actions need to be the same as your route names. This is how Simple API knows which action to call for which route. Each action receives three parameters: <code>req</code>, <code>res</code>, and <code>params</code> (naturally, you can name them anything). <code>req</code> and <code>res</code> are the HTTP request and response objects, respectively. The <code>params</code> object is a key-value object of all the matches from your route. For instance, <code>getCategoryTasks</code> will have <code>params.category</code> set from the second key in the URL. These keys will <em>always</em> be defined, otherwise the route would not have gotten called.
<pre><code>TasksController =
    options: {}
    routes: #Removed to save space
    actions:
        getAllTasks: (req, res, params) -&gt;
            Tasks = mongoose.model "Tasks" 

            Tasks.getAll (err, allTasks) =&gt;
                if err
                    console.log err
                    @responses.internalError res
                else
                    @responses.respond res, allTasks</code></pre>
Most of the code in that action is interacting with <a href="https://github.com/josephwegner/simple-example/blob/master/api/v0/models/tasks.coffee">the Model</a>, which is not important for this tutorial. Simple API is still early in its development, so it does <a href="https://github.com/josephwegner/simple-api#models">not officially support</a> any structured form of models. It will in the future, but for now you have to write your own models.

The important parts of this action are those two <code>@responses</code> lines. These are <a href="https://github.com/josephwegner/simple-api#convenience-functions">convenience functions</a> that have been defined so you don't have to write your own response handling. There are five convenience functions: <code>internalError</code>, <code>notAuth</code>, <code>notAvailable</code>, <code>redirect</code>, and <code>respond</code>. Each of them requires <code>res</code> as its first parameter, and accepts a response body as the second parameter. This response can either be a string or an object that will be turned into JSON.
<pre><code>getTask: (req, res, params) -&gt;
    Tasks = mongoose.model "Tasks"

    Tasks.getById params.identifier, (err, task) =&gt;
        if err
            console.log err
            @responses.internalError res
        else
            if task
                @responses.respond res, task
            else
                @responses.notAvailable res</code></pre>
This action is very similar to the <code>getAll</code> action, but it gets the task ID from <code>params.identifier</code>. As I mentioned, you don't need to check if the parameter exists. The action could not have been called if the parameter didn't exist. The rest of the actions will follow a similar format to these past two actions.
<pre><code>getCategoryTasks: (req, res, params) -&gt;
    Tasks = mongoose.model "Tasks"

    Tasks.getAllFromCategory params.category, (err, catTasks) =&gt;
        if err
            console.log err
            @responses.internalError res
        else
            @responses.respond res, catTasks

createTask: (req, res, params) -&gt;
    Tasks = mongoose.model "Tasks"

    data = ""

    req.on 'data', (chunk) -&gt;
        data += chunk

    req.on 'end', () =&gt;
        #You should do this in a try/catch, but I'm leaving it simple for the example
        taskInfo = JSON.parse data
        Tasks.create taskInfo, (err, task) =&gt;
            if err
                console.log err
                @responses.internalError res
            else
                @responses.respond res, task

updateTask: (req, res, params) -&gt;

    Tasks = mongoose.model "Tasks"

    data = ""

    req.on 'data', (chunk) -&gt;
        data += chunk

    req.on 'end', () =&gt;
        #You should do this in a try/catch, but I'm leaving it simple for the example
        taskInfo = JSON.parse data
        Tasks.updateById params.identifier, taskInfo, (err, task) =&gt;
            if err
                console.log err
                @responses.internalError res
            else
                @responses.respond res, task

deleteTask: (req, res, params) -&gt;
    Tasks = mongoose.model "Tasks"

    Tasks.deleteById params.identifier, (err) =&gt;
        if err
            console.log err
            @responses.internalError res
        else
            @responses.respond res

completeTask: (req, res, params) -&gt;
    Tasks = mongoose.model "Tasks"

    Tasks.getById params.identifier, (err, task) =&gt;
        if err
            console.log err
            @responses.internalError res
        else
            if task.completed
                #If the task is already completed, just return a 200 because it's already done
                @responses.respond res
            else
                task.completed = true
                task.save (err) =&gt;
                    if err
                        @responses.internalError res
                    else
                        @responses.respond res
</code></pre>
The only interesting difference here is in the <code>completeTask</code> action. In that action, you can see that I'm doing a bit more logic than in the other controllers. I'm first fetching the task from my model, then checking to see if it is already completed. If it is, I respond immediately - otherwise I update the task and send the response.
<h3>Helpers</h3>
If you were to run this project right now, you might notice one very major bug. I am using <a href="http://www.mongodb.org/">MongoDB</a> for the database, and using Mongo's built-in <a href="http://docs.mongodb.org/manual/reference/object-id/">ObjectId</a> type as the identifier. ObjectIds are 24 character alphanumeric strings, but they only use capital letters between A-F. Our current controller actions will accept any alphanumeric string as an ID, but Mongo throws an error if we send an incompatible string for an id. We <em>could</em> copy+paste some code into every action to make sure the IDs are valid, but it would probably be better to do that in a separate function. In order to keep your code organized, the cleanest place to put this is in the <code>helpers</code> object.
<pre><code>TasksController =
    options: {}
    routes: #Removed to save space
    actions: #Removed to save space
    helpers: 
        isValidID: (id) -&gt;
            id.match /^[0-9a-fA-F]{24}$/</code></pre>
The <code>isValidID</code> helper takes an id as a parameter, compares it to a RegExp <a href="https://github.com/mongodb/js-bson/blob/master/lib/bson/objectid.js#L15">written to match MongoDB ObjectID's</a>, and returns a boolean of their match state. When you're in a controller, <code>this</code> refers to the controller, so the helpers can be accessed in <code>this.helpers</code>. Therefore, <code>isValidID</code> can be accessed by <code>this.helpers.isValidID(id);</code>. We will need to update each of the actions that reference a task by ID with this helper.
<pre><code>getTask: (req, res, params) -&gt;

    if @helpers.isValidID params.identifier
        Tasks = mongoose.model "Tasks"

        Tasks.getById params.identifier, (err, task) =&gt;
            if err
                console.log err
                @responses.internalError res
            else
                if task
                    @responses.respond res, task
                else
                    @responses.notAvailable res
    else
        @responses.notAvailable res
</code></pre>
I've updated the <code>getTask</code> action so that it will continue the normal code flow if the <code>ObjectID</code> is valid, otherwise it will send a <code>notAvailable</code> response. The changes to the rest of the actions will be identical to this one, so I will leave them out. You can view the updated actions <a href="https://github.com/josephwegner/simple-example/blob/master/api/v0/controllers/tasks.coffee">in the repository</a>.
<h2>Where to Go From Here</h2>
It's finished! If you run your project using <code>coffee index</code>, you will have a fully functioning ToDo list API. I've written some cURL commands you can use to test the API.
<pre><code>#To create a new task
curl -X POST -d '{"name":"Create Example Project","category":"Simple Blog Post"}'

#To update an existing task (get the ID from the GET endpoints)
curl -X PUT -d '{"name":"Create An Example Project"}' http://localhost:3333/api/v0/tasks/52347045a543628e13000001

#To complete an existing task (get the ID from the GET endpoints)
curl -X PUT http://localhost:3333/api/v0/tasks/52347193bf6400d713000003/complete

#To delete an existing task (get the ID from the GET endpoints)
curl -X DELETE  http://localhost:3333/api/v0/tasks/52347154bf6400d713000001</code></pre>
Here are the important GET URLs:
<ul>
	<li>Get all tasks: <code>http://localhost:3333/api/v0/tasks<code></code></code></li>
	<li>Get a single task by ID: <code>http://localhost:3333/api/v0/tasks/52347154bf6400d713000001</code></li>
	<li>Get all tasks in a category: <code>http://localhost:333/api/v0/tasks/category/somecategory</code></li>
</ul>
I hope that you've enjoyed learning how to use Simple API. As I've mentioned a few times here and many times in the docs, Simple API is a <em>very new</em> library. It is being used in production in a few places, but it is far from done. I would greatly appreciate <a href="https://github.com/josephwegner/simple-api/issues?state=closed">feedback, bug reports, new ideas or comments</a> that you may have. Simple API will evolve with its users, so feel free to tell me how you would like it to work!