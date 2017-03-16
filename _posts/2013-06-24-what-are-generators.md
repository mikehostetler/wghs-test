---
ID: 504
post_title: What are Generators?
author: admin
post_date: 2013-06-24 11:21:09
post_excerpt: ""
layout: post
permalink: >
  http://modern-web.dev/2013/06/24/what-are-generators/
published: true
dsq_thread_id:
  - "2809874050"
om_counter:
  - "14"
---
*By [Toby Ho][1]* *Update: since this article was first published, the send() method has been removed from the spec. send(value) was replaced with calling next with a parameter value, i.e. next(value). I have updated this post with this change.* It looks like [generators][2] are actually going to make it into Javascript. In fact, generators recently landed in V8. This is so exciting! [Lately][3] [they've][4] [been][5] [all][6] [the][7] [rage][8]. But what is it? With this post I'll do my best to explain at a basic level what they are. 
## Get It Before we begin, let's make sure we can execute the code in the examples - because code is no good unless you can run it. You'll want to install an experimental version of Node (0.11.2+). I recommend following these steps: 

1.  Install [nvm][9]
2.  `nvm install v0.11.2` to install Node
3.  To run any of the examples, make sure you use the `--harmony` flag, i.e. `node --harmony` or `node --harmony myscript.js`

## The Basics At its simplest form, a generator represents a sequence of values - essentially it is an iterator. In fact, the interface of a generator object is a just an iterator for which you'd keep calling next() on it until it runs out of values. What makes a generator special is the way you write it: 

    function* naturalNumbers(){
      var n = 1;
      while (true){
        yield n++;
      }
    } In this example (

[source][10]), naturalNumbers is a *generator function* - or *generator* for short. It is written using the new * syntax as well as the new yield keyword introduced in ES6. This generator function returns *generator objects* that return the set of natural numbers in an infinite sequence. Each time yield is called, the yielded value becomes the next value in the sequence. To iterate through the sequence, a "for-of" syntax has also been proposed in ES6, however this is not yet been implemented in V8: 
    // this doesn't work yet. Also, it's an infinite loop ;P
    for (var number of naturalNumbers()){
      console.log('number is ', n);
    } Don't fret though, we can still use generators to do nice things. To create a new sequence, you'd call the generator function in order to get a live 

*generator object*: 
    > var numbers = naturalNumbers(); Now, if you call numbers.next(), you get an object with the properties 

`value` and `done`. 
    > numbers.next()
    { value: 0, done: false } The 

`value` property is the next value in the sequence, and `done` indicates whether or not the sequence has stopped; the sequence stops when the code has reach the end of the generator function. In the natural numbers example, the sequence would never stop, so to illustrate a stopped sequence let's make a simple sequence that just returns two numbers: 
    function* two(){
      yield 1;
      yield 2;
    } Now, let it run: 

    > var seq = two()
    > seq.next()
    { value: 1, done: false }
    > seq.next()
    { value: 2, done: false }
    > seq.next()
    { value: undefined, done: true } As you can see, on the third iteration, the 

`done` property is true and `value` is undefined. If we call it a fourth time, we'll get an exception: 
    > seq.next()
    Error: Generator has already finished

## Code Suspension The one thing that makes generators significant is that they make it possible to suspend code execution. Once you instantiate a generator object, you have a function whose execution can start and stop. Moreover, whenever it stops, you have control over 

*when* it restarts. To see this more concretely, let's create a generator that simply prints out strings to the console. 
    function* haiku(){
      console.log('I kill an ant');
      yield null; // the yield keyword requires a value, so I put null
      console.log('and realize my three children');
      yield null;
      console.log('have been watching.');
      yield null;
      console.log('- Kato Shuson');
    } Now, if we instantiate the generator object from this generator 

    > var g = haiku() and then iterate through it, line by line, by calling 

`g.next()` on the command line 
    > g.next() You can see that: 

1.  the code in the generator doesn't start executing until you say so;
2.  when the code encounters a yield statement, it suspends execution indefinitely, and doesn't start again until you tell it to. This opens possibilities for asychronicity in that you can call 

`next()` after some asynchronous event has occurred. To see this, take a look at the following example which makes the poem display a line per second by combining the generator with an [async loop][11]. 
    var g = haiku();
    function next(){
      if (g.next().done) return;
      setTimeout(next, 1000);
    }
    next(); Note that, while the code in the haiku generator looks like straight line Javascript, asychronicity has occurred in the middle of that code. This was not possible before due to Javascript's general single threaded nature. More specifically, each time a yield is encountered, asychronicity has a chance to occur. Those yields are kind of like time warping wormholes (

[source][12]). 
## Send Up until now we've looked at a generator objects only as a producer of a sequence of values, where information only goes one way: from the generator to you. It turns out that you can also send values to a generator by using the 

`next()` method, in which case the yield statement actually returns a value. Let's see this by making a generator that consumes values: 
    function* consumer(){
      while (true){
        var val = yield null;
        console.log('Got value', val);
      }
    } First instantiate a generator object: 

    > var c = consumer() Next, let's try calling 

`next()`: 
    > c.send(1)
    { value: null, done: false } This returns the expected object, but it also didn't 

`console.log()` anything, the reason being that initially, the generator wasn't yielding yet. If we call send again however, we'll see the `console.log` message: 
    > c.send(2)
    Got value 2
    { value: null, done: false }
    > c.send(3)
    Got value 3
    { value: null, done: false }

## Throw So we can both send and receive values from a generator, but guess what? You can also throw! 

    > c.throw(new Error('blarg!'))
    Error: blarg! When you 

`throw()` an error onto a generator object, the error actually propagates back up into the code of the generator, meaning you can actually use the `try` and `catch` statements to catch it. So, let's add try/catches to the last example: 
    function* consumer(){
      while (true){
        try{
          var val = yield null;
          console.log('Got value', val);
        }catch(e){
          console.log('You threw an error but I caught it ;P')
        }
      }
    } This time, once we instantiate the generator object, we'll call 

`next()` first because there's no way the generator can catch a error that's thrown at it before it even starts executing. 
    > var c = consumer()
    > c.next() From this point on, if we throw an error, it will catch the error and handle it gracefully: 

    > c.throw(new Error('blarg!'))
    You threw an error but I caught it ;P The try/catch worked. 

## Getting Fancy Now that you know the basic mechanics of generators, what can you 

*really* do with it? Well, most folks are excited about them because it is seen as the **Ticket Out of Callback Hell ™**. So let's see how that can be done. 
### The Premise In Javascript, especially in Node, IO operations are generally done as asynchronous operations that require a callback. When you have to do multiple async operations one after another it looks like this: 

    fs.readFile('blog_post_template.html', function(err, tpContent){
      fs.readFile('my_blog_post.md', function(err, mdContent){
        resp.end(template(tpContent, markdown(String(mdContent))));
      });
    }); It gets worse when you add in error handling: 

    
    fs.readFile('blog_post_template.html', function(err, tpContent){
      if (err){
        resp.end(err.message);
        return;
      }
      fs.readFile('my_blog_post.md', function(err, mdContent){
        if (err){
          resp.end(err.message);
          return;
        }
        resp.end(template(tpContent, markdown(String(mdContent))));
      });
    }); The promise of generators is that you can now write the equivalent code in a straight-line fashion 

    try{
      var tpContent = yield readFile('blog_post_template.html');
      var mdContent = yield readFile('my_blog_post.md');
      resp.end(template(tpContent, markdown(String(mdContent))));
    }catch(e){
      resp.end(e.message);
    } This is fantastic! Aside from less code and asthetics, it also has these benefits: 

*   *Line independence:* the code for one operation is no longer tied to the ones that come after it. If you want to reorder of operations, simply switch the lines. If you want to remove an operation, simply delete the line.
*   *Simpler and DRY error handling:* where as the callback-based style required error handling to be done for each individual async operation, with the generator-based style you can put one try/catch block around all the operations to handle errors uniformly. Generators give us back the power of try/catch exception handling.

### Make it Happen You've seen what we are aiming for, now how do we implement it? If you are the type who wants to figure it out for yourself, I don't want to ruin it for you. Stop reading. Code away. Come back. I'll wait. The first thing to realize is that the async operations need to take place outside of the generator function. This means that some sort of "controller" will need to handle the execution of the generator, fulfill async requests, and return the results back. So we'll need to pass the generator to this controller, for which we'll just make a 

`run()` function: 
    run(function*(){
      try{
        var tpContent = yield readFile('blog_post_template.html');
        var mdContent = yield readFile('my_blog_post.md');
        resp.end(template(tpContent, markdown(String(mdContent))));
      }catch(e){
        resp.end(e.message);
      }
    }); The 

`run()` function has the responsibility of calling the generator object repeatedly via `next()` and fulfill a request each time a value is yielded. It will assume that the requests it receives are functions that take a single callback parameter which takes `err` and `value` arguments, conforming to the Node style callback convention. When `err` is present, it will call `throw()` on the generator object to propagate it back into the generator's code path. The code for `run()` looks like: 
    function run(genfun){
      // instantiate the generator object
      var gen = genfun();
      // This is the async loop pattern
      function next(err, answer){
        var res;
        if (err){
          // if err, throw it into the wormhole
          return gen.throw(err);
        }else{
          // if good value, send it
          res = gen.send(answer);
        }
        if (!res.done){
          // if we are not at the end
          // we have an async request to
          // fulfill, we do this by calling 
          // `value` as a function
          // and passing it a callback
          // that receives err, answer
          // for which we'll just use `next()`
          res.value(next);
        }
      }
      // Kick off the async loop
      next();
    } Now given that, 

`readFile` takes the file path as parameter and needs to return a function: 
    function readFile(filepath){
      return function(callback){
        fs.readFile(filepath, callback);
      }
    } And that's it! If that went too fast, feel free to poke at the 

[full source code][13]. 
## Where to go from here To learn more about generators: 

*   The original post by [wingo][8] when generators made it into V8
*   [T.J. Holowaychuk's post][4] and his [co library][14] which is in concept similar to the `run` function I've demonstrated
*   [Suspend][7] and [genny][15] take a slightly different approach and has different tradeoffs than something like co
*   [task.js][16] - Dave Herman's task library
*   [Q][17] is a promise library which supports generators
*   A [brilliant tutorial][18] on generators in python by David Beazley
*   The [original proposal][19] that introduced generators to Python

*This article was orginally published at <http://tobyho.com/2013/06/16/what-are-generators/>*

 [1]: /authors/toby-ho
 [2]: http://wiki.ecmascript.org/doku.php?id=harmony:generators
 [3]: http://dailyjs.com/2013/05/31/suspend/
 [4]: https://medium.com/code-adventures/174f1fe66127
 [5]: http://jlongster.com/A-Study-on-Solving-Callbacks-with-JavaScript-Generators
 [6]: https://gist.github.com/creationix/5761021
 [7]: https://github.com/jmar777/suspend
 [8]: http://wingolog.org/archives/2013/05/08/generators-in-v8
 [9]: https://github.com/creationix/nvm
 [10]: https://github.com/airportyh/generators_hello/blob/master/iteration.js
 [11]: http://tobyho.com/2011/11/03/delaying-repeatedly/
 [12]: https://github.com/airportyh/generators_hello/blob/master/haiku.js
 [13]: https://github.com/airportyh/generators_hello/tree/master/blog
 [14]: https://github.com/visionmedia/co/
 [15]: https://github.com/spion/genny
 [16]: http://taskjs.org/
 [17]: https://github.com/kriskowal/q
 [18]: http://www.dabeaz.com/coroutines/index.html
 [19]: http://www.python.org/dev/peps/pep-0255/