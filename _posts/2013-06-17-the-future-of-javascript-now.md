---
ID: 464
post_title: The Future of JavaScript…Now!
author: admin
post_date: 2013-06-17 12:12:11
post_excerpt: ""
layout: post
permalink: >
  http://modern-web.dev/2013/06/17/the-future-of-javascript-now/
published: true
dsq_thread_id:
  - "2809873954"
om_counter:
  - "77"
---
*By [Joe Zimmerman][1]* JavaScript has come a very long way in the past decade. What was previously known as a hobby language for throwing silly tricks like image rollovers and mouse cursor trails onto web pages has become the most widely used programming language on the most widely accessed platform: the Internet. JavaScript has gone from an extra thing you tack onto your page at the end to being a requirement for nearly every web project. But its popularity doesn't mean the language is great. In fact, as more and more people use JavaScript, we become more aware of its quirks and problems. JavaScript must evolve, and it is in the process of doing so, but until the[ ECMAScript 6][2] standard is finalized and all major browsers support its new features, we're still stuck with ES5… or are we? As you read this, you can already write your code using the to-be-finalized ES6 standard and still have your code run on all major browsers. How? Transpiling. Transpiling is the process of compiling code from one language into the code of another language, which in this instance means from ES6 to ES5. This isn't the only option, though. I will be discussing two other languages that transpile into JavaScript that you can start using today: CoffeeScript and TypeScript. There are numerous other languages that you can use, including C++ in some scenarios, but I won't be discussing them here. 
## ECMAScript 6 ECMAScript 6 (or ES6 for short) is the next version of JavaScript that is in the works. It's the first large revision that JavaScript has had in a very long time. There are a plethora of new features, many of which are just syntactic sugar, which just means that the feature allows us to do the same things we've always done except with a much cleaner, more concise syntax. For instance, there is the new 

`class` construct. Take a look at a code sample: 
    class ChildClass extends ParentClass {
        constructor() {
            // Initialization
            super(); // Call super class functions
        }
    
        someMethod(arg) {
            // Do something you lazy bum
        }
    } While the above example still uses prototypal inheritance behind the scenes, it eliminates the mess of code that is required for some of the features. Notice we are extending a parent class simply by saying that 

`ChildClass extends ParentClass`. This is the same as `ChildClass.prototype = new ParentClass()` except more concise and doesn't require an instantiation of `ParentClass`, which avoids potential side effects from the computations within the `ParentClass` constructor. Also, you can see that I called `super()` in my constructor. The ability to call the parent class' functions like this is not nearly as simple as it should be with our current inheritance syntax limitations, but ES6 makes it extremely simple with this little bit of syntactic sugar. Many people have argued that we shouldn't have this `class` construct because it makes JavaScript appear to have classical inheritance, which is quite different from the prototypal inheritance running behind the scenes here. But a developer needs to be able to work quickly and the overhead associated with the current prototypal syntax slows us down. It's the developer's responsibility to know how the language works as much as possible. ES6 brings to the table several syntactic sugar features like this and some other wonderful features, such as `let`, which replaces the `var` keyword to give a variable block scope (rather than function scope). There are[ plenty of posts][3][ about the new features][4] coming in ES6, so if you're curious, check them out. Many features are only useful in certain situations, but they'll likely come in handy for you at some point. 
## TypeScript TypeScript is a language by Microsoft. Whatever your feelings to the big "M" might be, you should still at least take a quick look at TypeScript. TypeScript is a superset of JavaScript, which means that you can write regular JavaScript if you want and it'll work. TypeScript adds plenty of features though. First off, it inherits all of the ES6 features, so it's not just a superset of the current JavaScript, but also of the future version of JavaScript. 

[<img class="alignnone size-full wp-image-465" alt="Typescript" src="http://flippinawesome.org/wp-content/uploads/2013/06/Typescript.png" width="697" height="367" />][5] Figure 1: Type Hints in Action The other big feature that it gives you is interfaces and type declarations (hence the "Type" in the language name). The entire point of this is to help with building larger applications by allowing your IDE to know what types should be used and give you type hints and warnings when you use the wrong types. Those coming from strongly-typed languages will appreciate this feature. I think that my favorite part about TypeScript's implementation of this feature is that it's completely transparent once it's been compiled back to JavaScript. The types are used solely during development to make sure you don't make any mistakes. Once the code is transpiled, there's no evidence that you were checking for types at all, not even[ duck-typing][6] checks. This keeps the code short and clean. In the code sample below, you can see what I mean: 
    /* Here’s a simple bit of TypeScript */
    class Greeter {
        private greeting;   
        constructor(greeting: string) { // ": string" limits the arguments to strings
            this.greeting = greeting;
        }
        greet() {
            return "Hello, " + this.greeting;
        }
    }
    
    var greeter = new Greeter("world");
    
    /* Here’s the compiled JavaScript */
    var Greeter = (function () {
        function Greeter(greeting) { // Notice no type limitations
            this.greeting = greeting;
        }
        Greeter.prototype.greet = function () {
            return "Hello, " + this.greeting;
        };
        return Greeter;
    })();
    var greeter = new Greeter("world"); Microsoft has also created several plugins for code editors and IDE's that give them the ability to understand TypeScript so you can have type hints in your favorite editor and be able to compile to JavaScript without the command-line. There's plenty to explore so check out

[ their website][7], use their online IDE – called the[ Playground][8] – to practice and test it out yourself, and read their lengthy, but pretty clear[ documentation][9]. 
## CoffeeScript CoffeeScript borrows a lot of its philosophy from Ruby. It tries to do away with unnecessary syntax such as semi colons and parentheses (as often as possible). Back during the big "

[Semicolon Debate][10]", CoffeeScript added fuel to the arguments of those on the side that declared semicolons were pointless. If you're one of the people who want JavaScript – except possibly more powerful – CoffeeScript isn't for you. If you're one of the people who hates JavaScript and all of its quirks, this is probably your best alternative, especially if you prefer syntax to get out of the way and let you work. CoffeeScript comes with a plethora of interesting and awesome features. Just take a look at the Overview section on[ the home page][11] and you'll find several things standing out, such as:[ postfix conditionals][12],[ arrow functions][13], meaningful whitespace,[ splats][14] and more. Some of these features are actually showing up in ES6, but CoffeeScript is often heralded as the reason ES6 has decided to add the features. Before languages that transpiled to JavaScript existed, JavaScript's real competition had been practically zero, but now, in large part due to CoffeeScript, we're seeing more diversity, which is pushing the innovation of JavaScript. Even though I'm not personally a fan of CoffeeScript's lack of syntax, I'm very grateful for what it has done for JavaScript. 
## Transpiling to JavaScript Each of the languages mentioned above offer a lot to developers for improving the development experience of web applications. Personally, I'm excited about each of these projects and I hope each one continues to become even better. But until these languages are supported natively in all major browsers (I wouldn't count on anything other than ES6 obtaining that support), we still need to convert this code to usable JavaScript. Each language has its own compiler(s): 

*   ECMAScript 6:[ traceur][15],[ ES6 Module Transpiler][16] (for converting ES6 modules to AMD or CommonJS modules)
*   TypeScript:[ TypeScript Compiler][17]
*   CoffeeScript:[ CoffeeScript Compiler][18] Each of the tools just listed also has the ability to generate

[ source maps][19], which allow you to debug using the code in the language you wrote it in, instead of being required to debug the compiled JavaScript code. Check out each project's documentation to find out how to generate source maps with them. 
## Integrate into Your Workflow with Grunt If you love the idea of using the features of these languages but hate the idea of going to the command-line and compiling the code each time you make a change, there's a solution for you:

[ Grunt][20]. Chances are, you've heard of Grunt by now and may have even used it. If you've never heard of it before or used it, their[ "Getting Started" guide][21] is pretty helpful. I won't be going into any details on how to use Grunt, but I will tell you that you can set Grunt to watch for changes to your files and immediately compile them after they're saved. Most of these Grunt plugins can also add source maps (I've indicated the ones that don't currently have the capability). 
*   ECMAScript 6:[ grunt-traceur][22] (no source maps),[ grunt-es6-module-transpiler][23] (no source maps)
*   TypeScript:[ grunt-typescript][24]
*   CoffeeScript:[ grunt-contrib-coffee][25] Just check out the documentation, install the Grunt plugins (and Grunt too, of course), configure, and you're on your way to writing awesome code. 

## Other Languages I've only mentioned a few languages here that are pushing web development to new heights, but there are several others.

[ Dart][26], which was developed by Google, is a language that Google plans to integrate directly into Chrome, but it also was designed from the beginning to be able to compile down to JavaScript. Other languages that have existed for a long time are also starting to be compiled to JavaScript, such as Java and C++. WebGL has allowed 3D games to become feasible and Mozilla has been working hard to allow developers to create games in C++ and convert them to JavaScript. For an example of what is possible, check out this demo game that was created in C++ but running in the browser:[ BananaBread][27]. 
## Conclusion All-in-all, the future of web development, and JavaScript in general, looks bright. Even if developers don't like JavaScript, they can still code for the web. And with all the tools coming out, development is getting faster and more automated, too. We're all lucky to be witnessing this era in web development and I suspect the future will be even better.

 [1]: /authors/joe-zimmerman
 [2]: http://wiki.ecmascript.org/doku.php?id=harmony:specification_drafts
 [3]: http://www.2ality.com/2012/11/guide-esnext.html
 [4]: http://addyosmani.com/blog/ecmascript-6-resources-for-the-curious-javascripter/
 [5]: http://flippinawesome.org/wp-content/uploads/2013/06/Typescript.png
 [6]: http://stackoverflow.com/a/3379721
 [7]: http://www.typescriptlang.org/
 [8]: http://www.typescriptlang.org/Playground/
 [9]: http://go.microsoft.com/fwlink/?LinkId=267238
 [10]: http://www.joezimjs.com/javascript/the-great-semicolon-debate/
 [11]: http://coffeescript.org/
 [12]: http://coffeescript.org/#conditionals
 [13]: http://coffeescript.org/#literals
 [14]: http://coffeescript.org/#splats
 [15]: http://code.google.com/p/traceur-compiler/
 [16]: http://square.github.io/es6-module-transpiler/
 [17]: http://www.typescriptlang.org/#Download
 [18]: http://coffeescript.org/#installation
 [19]: http://www.thecssninja.com/javascript/source-mapping
 [20]: http://gruntjs.com/
 [21]: http://gruntjs.com/getting-started
 [22]: https://npmjs.org/package/grunt-traceur
 [23]: https://npmjs.org/package/grunt-es6-module-transpiler
 [24]: https://npmjs.org/package/grunt-typescript
 [25]: https://npmjs.org/package/grunt-contrib-coffee
 [26]: http://www.dartlang.org/
 [27]: https://developer.mozilla.org/en-US/demos/detail/bananabread