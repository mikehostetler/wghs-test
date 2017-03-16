---
ID: 366
post_title: Fluent APIs and Method Chaining
author: admin
post_date: 2013-05-20 13:09:19
post_excerpt: ""
layout: post
permalink: >
  http://modern-web.dev/2013/05/20/fluent-apis-and-method-chaining/
published: true
dsq_thread_id:
  - "2809873636"
om_counter:
  - "44"
---
*By [Eric Feminella][1]* Of the vast catalog of design patterns available at our disposal, often times I find it is the simpler, less prominent patterns which people use quite frequently, yet receive much less recognition. A good example of this is the [Method Chaining Pattern][2]. The Method Chaining Pattern, as I have come to appreciate it over the years, offers a means of facilitating expressiveness and fluency when used articulately, and offers convenience in it's less sophisticated use-cases. 
## Design Considerations When considering Method Chaining, you should take heed not to simply use the pattern as just syntactic sugar that allows you to write fewer lines of code. Rather, Method Chaining should be used, perhaps more appropriately, as a means of implementing 

[Fluent APIs][3] which allow for writing more concise expressions. These expressions can be written, and read, in much the same way as natural language, though they need not be the same from a truly lexical perspective. The resulting terseness afforded by Method Chaining, while convenient, is in most cases not in-of-itself a reason alone for leveraging the pattern. 
## Implementation Method Chaining, from a purely implementation perspective, is perhaps the simplest of all design patterns. It's basic mandate simply prescribes returning a reference to the object on which a method is being called (in most languages, JavaScript in particular, the this pointer). Consider the following, intentionally contrived, example: 

    var Chain = function(val){
        this.init(val);
    };
    var fn = Chain.prototype;
    fn.init = function(val){
        this.links = isNaN(val) ? 0 : val;
    };
    fn.addLinks = function(val){
        if (!isNaN(val)) {
            this.links += val;
            this.links = this.links < 0 ? 0 : this.links;
        }   
        return this;
    };
    fn.removeLinks = function(val){
        if (!isNaN(val)) {
            this.links -= val;
            this.links = this.links < 0 ? 0 : this.links;
        }   
        return this;
    };
    
    var chain = new Chain();
    console.log(chain.addLinks(10).removeLinks(5).links); // 5
     As you can see, implementing Method Chaining requires nothing more than simply having methods return a reference to this. 

## API Simplicity Method Chaining is typically used when breaking from traditional 

[Command Query Separation][4] (CQS) principles. The most common example being merging of both getters (Queries) and setters (Commands). I especially like this technique as, aside from being very easy to implement, it allows for an API to be used in a more contextual manner from the developers perspective rather than the API designer's preconceptions of how the API will be used. For example: 
    var Messenger = function(msg){
        this.val = msg || '';
    };
    var fn = Messenger.prototype;
    fn.message = function(msg){
        if (msg){
            this.val += msg + ' '; 
            return this;
        }
        return this.val;
    };
    console.log(new Messenger().message('Hello, World').
    .message()); // Hello World As can be seen, the “message” method serves as both a getter and setter, allowing users of the API to determine how the method should be invoked based on context, as well as affording developers the convenience of needing only to remember a single method name. This technique is used quite heavily in many JavaScript libraries and has undoubtedly contributed to their success. We could further expand on this concept by determining a method's invocation context based on the arguments provided, or the types of specific arguments, thus merging various similar methods based on a particular context. An important design recommendation to consider is that if you are writing an API which violates CQS (which is quite fine IMHO) API consistency is important, thus all getters and setters should be implemented in the same manner. 

## Fluency In most cases, Method Chaining is leveraged to facilitate APIs which are intended to be used fluently (e.g. an 

[Internal DSL][5]). Such implementations typically provide methods which, by themselves, may have little meaning. However, when combined, they allow for writing expressions which are self-describing and make logical sense to users of the API. For example, consider the way one might describe a Calendrical Event: *Vacation, begins June 21st, ends July 5th, recurs Yearly.* We can easily implement a Fluent API such that the above grammar can be emulated in code as follows: 
    var Event = function(name){
        ...
    };
    var fn = Event.prototype;
    fn.begins = function(time){
        ...
        return this;
    };
    fn.ends = function(time){
        ...
        return this;
    };
    fn.recurs = function(when){
        ...
        return this;
    };
    var vacation = new Event('Vacation');
    vacation.begins('June 21st'').ends('July 5th').recurs('Yearly'); The same methods can also be chained in different combinations, yet yield the same value: 

    var vacation = new Event('Vacation');
    vacation.recurs('Yearly').ends('July 5th').begins('June 21st''); Given the above example, we could further improve on the fluency of the implementation by adding intermediate methods which can serve to aid in readability or provide an alternate modifier for chaining: 

    var Event = function(name){
            ...
    };
    var fn = Event.prototype;
    fn.begins = function(time){
        ...
        return this;
    };
    fn.ends = function(time){
        ...
        return this;
    };
    fn.recurs = function(when){
        ...
        return this;
    };
    fn.on = function(when){
        ...
        return this;
    };
    fn.at = function(when){
        ...
        return this;
    };
    fn.every = function(when){
        ...
        return this;
    };
    
    var vacation = new Event('Vacation');
    vacation.begins.on('June 21st').at('5pm').ends.on('July 5th').at('11pm').recurs.every('Year'); When implementing Fluent APIs, we can design them to allow different logical chaining combinations that can yield the same result, thus affording users of the API the convenience of determining the most appropriate expressions based on context or personal preference. Illogical chaining combinations can be handled by either throwing an exception, or they can simply be ignored based on the context of a preceding invocation – though, of course, one should aim to avoid designs which allow for illogical chaining. 

## The Ubiquitous Example – jQuery Method Chaining and Fluent APIs, as with most design patterns, are language agnostic. In the JavaScript world perhaps the most well known implementation is the 

[jQuery API][6]; for example: 
    $('#test').css('color','#333').height(200); In addition to jQuery, there are numerous additional JavaScript Method Chaining and Fluent APIs of note., 

[Jasmine][7] in particular has a very expressive API which aligns excellently with it's design goals. The various libraries which implement the [Promises/A spec][8] also provide very clear and concise Fluent APIs. 
## Concluding Thoughts Over the years I have leveraged Method Chaining to facilitate the design of Fluent APIs for various use-cases. The two patterns, when combined, can be especially useful when designing Internal DSLs; either third-party libraries, or APIs specific to a particular business domain. 

*This article was originally published at <http://www.ericfeminella.com/blog/2013/05/13/fluent-apis-and-method-chaining/>*

 [1]: /authors/eric-feminella
 [2]: http://martinfowler.com/dslCatalog/methodChaining.html
 [3]: http://www.martinfowler.com/bliki/FluentInterface.html
 [4]: http://en.wikipedia.org/wiki/Command-query_separation
 [5]: http://msdn.microsoft.com/en-us/magazine/ee291514.aspx
 [6]: http://api.jquery.com/
 [7]: http://pivotal.github.com/jasmine/
 [8]: http://wiki.commonjs.org/wiki/Promises/A