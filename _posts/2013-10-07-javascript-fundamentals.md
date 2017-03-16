---
ID: 1058
post_title: JavaScript Fundamentals
author: admin
post_date: 2013-10-07 12:13:00
post_excerpt: ""
layout: post
permalink: >
  http://modern-web.dev/2013/10/07/javascript-fundamentals/
published: true
dsq_thread_id:
  - "2809887729"
om_counter:
  - "79"
---
*By [Azat Mardanov][1]* 
> If it's not fun, it's not JavaScript. And you can't spell fundamentals without fun. Programming languages like BASIC, Python and C have a boring, machine-like nature that requires developers to write extra code that's not directly related to the solution itself. Think about line numbers in BASIC or interfaces, classes and patterns in Java. On the other hand JavaScript inherits the best traits of pure mathematics, LISP and C# which leads to a great deal of [expressiveness][2] (and, arguably, fun). You can read more about expressive power in languages via [this post][3]. The quintessential "Hello World" example in Java looks like (remember, Java is to JavaScript is what ham to a hamster): 
    public class HelloWorld {
        public static void main(String[] args) {
            System.out.println("Hello World");
        }
    } The same example in JavaScript: 

    console.log('Hello World') ...or from within an HTML page: 

    <script>
    document.write('Hello World')
    </script> JavaScript allows programmers to focus on the solution/problem rather that to jump through hoops and API docs. In this article I want to cover some of the basic aspects of JavaScript that can make developing with it fun (if occassionally also frustrating). 

## Loose Typing Automatic type casting works well most of the time. It's a great feature that saves a lot of time and mental energy. There are only a few primitives types in JavaScript: 

1.  String
2.  Number (both integer and real)
3.  Boolean
4.  Undefined
5.  Null Everything else is an object (i.e., mutable keyed collections). Read Stackoverflow on 

["What does immutable mean?"][4] Also, in JavaScript, there are `String`, `Number` and `Boolean` objects which contain helpers for the primitives. For example: 
    'a' === new String('a') //false ...but... 

    'a' === new String('a').toString() //true ...or... 

    'a' == new String('a') //true By the way, 

`==` performs automatic type casting while `===` does not. 
## Object Literal Notation Object notation in JavaScript is very readable and compact: 

    var obj = {
       color: "green",
       type: "suv",
       owner: {
          ...
       }
    } Remember that functions are objects so you could also write: 

    var obj = function () {
    // do something
    }; 
    obj.a =1; 
    console.log (obj.a); // prints 1 Another very similar looking pattern (but completely different in behavior) is when we use function to construct/initialize objects: 

    var Obj = function (ops) {
      this.name = ops.name;
    }
    var obj = new Obj ({name: 'puppy'});
    console.log(obj)

## Functions Functions are 

*first-class citizens*, and we treat them as variables, because they are objects. Yes, functions can even have properties/attributes. 
### Create a Function

    var f = function f () {
       console.log('Hi');
       return true;
    } ...or... 

    function f () {
      console.log('Hi');
      return true;
    } A function with a property (remember functions are just objects that can be invoked, i.e. initialized): 

    var f = function () {console.log('Boo');}
    f.boo = 1;
    f(); //outputs Boo
    console.log(f.boo); //outputs 1

**Note**: the return keyword is optional. In this case it's omitted and the function will return undefined upon invocation. 
### Passing Functions as Parameters Because of the special nature of functions in JavaScript, you can also pass a function as a parameter to another function: 

    var convertNum = function (num) {
     return num + 10;
    }
    
    var processNum = function (num, fn) {
       return fn(num);
    }
    
    processNum(10, convertNum);

## Arrays Arrays in JavaScript are also objects which have some special methods inherited from the 

[Array.prototype][5] global object. Nevertheless, JavaScript Arrays are not real arrays. Instead, they are objects with unique integer (usually 0-based) keys. 
    var arr = [];
    var arr2 = [1, "Hi", {a:2}, function () {console.log('boo');}];
    var arr3 = new Array();
    var arr4 = new Array(1,"Hi", {a:2}, function () {console.log('boo');});

## Prototypal Nature There are no classes in JavaScript because objects inherit directly from other objects which is called prototypal inheritance. However, there are a few types of inheritance design patterns used by JavaScript developers: 

*   Classical
*   Pseudo-classical
*   Functional Here's an example of the functional inheritance pattern: 

    var user = function (ops) {
      return { firstName: ops.name || 'John'
             , lastName: ops.name || 'Doe'
             , email: ops.email || 'test@test.com'
             , name: function() { return this.firstName + this.lastName}
             }
    }
    
    var agency = function(ops) {
      ops = ops || {}
      var agency = user(ops)
      agency.customers = ops.customers || 0
      agency.isAgency = true
      return agency
    } While not technically classes, they are often referred to as such. 

## Conventions Most of these conventions (with semi-colons being an exception) are stylistic, highly preferential and don't impact the execution. 

### Semi-Colons Semi-colons are optional except for two cases: 

1.  In for loop construction: for (var i=0; i++; i<n)
2.  When a new line starts with parentheses, as in Immediately-Invoked Function Expressions (IIFE): `;(function(){...}())`

### camelCase Use lowerCamelCase, except for "class" names which are UpperCamelCase: 

    var MainView = Backbone.View.extend({...})
    var mainView = new MainView()

### Variable Naming

`_` and `$` are perfectly legitimate characters for literals (the jQuery and Underscore libraries obviously use them a lot). Private methods and attributes within "classes" should start with `_`. 
### Commas Comma-first approach 

    var obj = { firstName: "John"
              , lastName: "Smith"
              , email: "johnsmith@gmail.com"
              }

### Indentation Usually JavaScript developers use either tab, 4 or 2 space indentation with each their supporters' camps being almost religiously split. 

### White spaces Usually, there is a space before and after 

`=`, `+`, ans the `{` and `}` symbols. There is no space on function invocation, as in `arr.push(1);`, but there's typically a space when we define an anonymous function as in `function () {}`. 
## Modules There is no built in support for modules, at least until 

[ES6][6] (i.e. ECMAScript 6 which is the next version of JavaScript). Everything is in the global scope (a.k.a. `window`) and included via `<script>` tags. However, there are external libraries that allow for workarounds to support module loading: 
*   [CommonJS][7]
*   AMD and [Require.js][8] Node.js uses a CommonJS-like syntax and has built-in support for modules. To hide your code from the global scope, you can make private attributes/methods using closures and 

[immediately-invoked function expressions][9] (commonly called IIFEs). 
### Immediately-Invoked Function Expressions (IIFEs) A basic IIFE with no implementation is: 

    (function () {
    window.yourModule = {
    ...
    };
    }()); This snippet show an example of a object with a private attribute and a method: 

    (function () {
      window.boo = function() {
        var _a = 1;
        var inc = function () {
          _a++; 
          console.log(_a);
          return _a;
        };
        return {
          increment: inc
        }; 
      }
    }());
    var b = window.boo();
    b.increment(); Now try this: 

    b.increment();
    b.increment();
    b.increment();

## “this” Keyword

`this` mutates/changes a lot (especially in jQuery). A common pattern is to re-assign it to a locally scoped variable before attempting to use `this` inside of a closure: 
    var app = this
    $('a').click(function(e){
      console.log(this) //most likely the event or the      target anchor element
      console.log(app) //that's what we want!
      app.processData(e)
     })

*When in doubt, use `console.log`!* 
## Pitfalls JavaScript is the only language I know of that programmers often think they shouldn't truly learn (instead often relying on things like libraries and copy/paste to get by). However, things like 

`===` versus `==`, global scope leakage and other aspects of the language might lead to problems down the road. This is why it's important to understand the language or use an abstraction like CoffeeScript, that abstracts away most of the issues. 
## Further Learning If you liked this article and would like to explore JavaScript more, take a look at my free resource: 

[Eloquent JavaScript: A Modern Introduction to Programming][10]. Of course for more advanced JavaScript enthusiasts and pros, there’s my book [Rapid Prototyping with JS][11] and intensive programming school [HackReactor][12], where I teach part-time. *This article was originally published at <http://webapplog.com/js-fundamentals/>*

 [1]: /authors/azat-mardanov
 [2]: http://en.wikipedia.org/wiki/Expressive_power
 [3]: http://stackoverflow.com/questions/638881/what-does-expressive-mean-when-referring-to-programming-languages
 [4]: http://stackoverflow.com/questions/3200211/what-does-immutable-mean
 [5]: https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array/prototype#Properties
 [6]: https://wiki.mozilla.org/ES6_plans
 [7]: http://www.commonjs.org/
 [8]: http://requirejs.org/
 [9]: http://en.wikipedia.org/wiki/Immediately-invoked_function_expression
 [10]: http://eloquentjavascript.net/
 [11]: http://rpjs.co/
 [12]: http://hackreactor.com/