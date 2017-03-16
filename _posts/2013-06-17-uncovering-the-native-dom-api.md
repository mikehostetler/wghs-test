---
ID: 486
post_title: Uncovering the Native DOM API
author: admin
post_date: 2013-06-17 12:11:41
post_excerpt: ""
layout: post
permalink: >
  http://modern-web.dev/2013/06/17/uncovering-the-native-dom-api/
published: true
dsq_thread_id:
  - "2809873047"
om_counter:
  - "17"
---
*By [Nicolas Bevacqua][1]* JavaScript libraries such as jQuery serve a great purpose in normalizing cross-browser behaviors in the <a href="https://developer.mozilla.org/en/docs/DOM" rel="nofollow">DOM</a> in such a way that it's possible to use the same interface to interact with many different browsers. But they do so at a price. Part of that price, in the case of some developers, is having no idea what the heck the library is actually doing when we use it. Heck, it works! Right? Well, no. You should know what's happening behind the scenes, in order to better understand what you are doing. Otherwise, you would be just <a href="http://pragprog.com/the-pragmatic-programmer/extracts/coincidence" rel="nofollow">programming by coincidence</a>. In this article, I'll explore some of the parts of the DOM API that are usually abstracted away behind a little neat interface in your library of choice, including Ajax, events and DOM querying. 
## Meet: XMLHttpRequest Surely you know how to write AJAX requests, right? Probably something like... 

    $.ajax({
        url: '/endpoint'
    }).done(function(data){
        // do something awesome
    }).fail(function(xhr){
        // sad little dance
    }); But, how do we write that with native browser-level JavaScript? The name XMLHttpRequest is right on one count in that it performs requests. But the name is misleading in that it can handle any data, not just XML. It also is't limited to just the 

<a href="http://en.wikipedia.org/wiki/Hypertext_Transfer_Protocol" rel="nofollow">HTTP protocol</a>. For full details, check out <a href="https://developer.mozilla.org/en-US/docs/Web/API/XMLHttpRequest" rel="nofollow">MDN</a>. XMLHttpRequest is what drives the AJAX "magic" all over rich internet applications nowadays. Writing these requests can be, admittedly, kind of hard to get right without looking it up, or having prepared to use them for an interview. Lets give it a first try: 
    var xhr = new XMLHttpRequest();
    xhr.onreadystatechange = function(){
        var completed = 4;
        if(xhr.readyState === completed){
            if(xhr.status === 200){
                // do something with xhr.responseText
            }else{
                // handle the error
            }
        }
    };
    xhr.open('GET', '/endpoint', true);
    xhr.send(null); You can also test this in a pen I made 

<a href="http://cdpn.io/ycgzo" rel="nofollow">here</a>. Let's go over the snippet I wrote here, making sure we don't miss anything. The `.onreadystatechange` handler will fire every time `xhr.readyState` changes, but the only state that's really relevant here is 4, which denotes an XHR request is complete, regardless of the outcome. Once the request is complete, the XHR object will have it's status filled. If you try to access status before completion, you might <a href="http://stackoverflow.com/a/15623060/389745" rel="nofollow">get an exception</a>. Lastly, once you know the status of your XHR request, you can do something about it, you should use `xhr.responseText` to figure out how to react to the response, probably passing that to a callback. The request is prepared using `xhr.open`, passing the <a href="http://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html" rel="nofollow">HTTP method</a> in the first parameter, the resource to query in the second parameter, and a third parameter to decide whether the request should be asynchronous (true) or block the UI thread and make everyone cry (false). If you also want to send some data, you should pass that to `xhr.send`. This function actually <a href="https://developer.mozilla.org/en-US/docs/Web/API/XMLHttpRequest#send%28%29" rel="nofollow">sends the request</a> and it supports all the signatures below. 
    void send();
    void send(ArrayBuffer data);
    void send(Blob data);
    void send(Document data);
    void send(DOMString? data);
    void send(FormData data); I won't go into detail, but you'd use those signatures to send data to the server. A sensible way to wrap our native XHR call in a reusable function might be the following: 

    function ajax(url, opts){
        var xhr = new XMLHttpRequest();
        xhr.onreadystatechange = function(){
            var completed = 4;
            if(xhr.readyState === completed){
                if(xhr.status === 200){
                    opts.success(xhr.responseText, xhr);
                }else{
                    opts.error(xhr.responseText, xhr);
                }
            }
        };
        xhr.open(opts.method, url, true);
        xhr.send(opts.data);
    }
    ajax('/foo', { // usage
        method: 'GET',
        success: function(response){
            console.log(response);
        },
        error: function(response){
            console.log(response);
        }
    }); You might want to add default values to the method, success and error options, maybe even use 

[promises][2], but this should be enough to get you going. 
## Event Listeners Lets say you now want to attach that awesome AJAX call to one your DOM elements, that's ridiculously easy! 

    $('button').on('click', function(){
        ajax( ... );
    }); Sure, you could use jQuery like your life depended on it, but this one is pretty simple to do with "pure" JavaScript. Lets try a reusable function from the get-go. 

    function add(element, type, handler){
        if (element.addEventListener){
            element.addEventListener(type, handler, false);
        }else if (element.attachEvent){
            element.attachEvent('on' + type, handler); 
        }else{
            // more on this later
        }
    }
    function remove(element, type, handler){
        if (element.removeEventListener){
            element.removeEventListener(type, handler);
        }else if (element.detachEvent){
            element.detachEvent(type, handler);
        }else{
            // more on this later
        }
    } This one is pretty straightforward, you just add events with either the 

<a href="http://www.w3.org/TR/DOM-Level-2-Events/events.html" rel="nofollow">W3C event model</a> or the <a href="http://msdn.microsoft.com/en-us/library/ie/ms536343%28v=vs.85%29.aspx" rel="nofollow">IE event model</a>. The <a href="http://msdn.microsoft.com/en-us/library/ms533023%28v=vs.85%29.aspx" rel="nofollow">last resort</a> would be to use an element['on' + type] = handler, but this would be very bad because we wouldn't be able to attach more than one event to each DOM element. If necessary, we could use a dictionary to keep the handlers in a way that they are easy to add and remove. Then it would be just a matter of calling all of these handlers when an event is fired. This brings a whole host of complications, though: 
    function(window){
        var events = {}, map = [];
        function add(element, type, handler){
            var key = 'on' + type,
                id = uid(element),
                e = events[id];
            element[key] = eventStorm(element, type);
            if(!e){
                e = events[id] = { handlers: {} };
            }
            if(!e.handlers[type]){
                e.handlers[type] = [];
                e.handlers[type].active = 0;
            }
            e.handlers[type].push(handler);
            e.handlers[type].active++;
        }
        function remove(element, type, handler){
            var key = 'on' + type,
                e = events[uid(element)];
            if(!e || !e.handlers[type]){
                return;
            }
            var handlers = e.handlers[type],
                index = handlers.indexOf(handler);
            // delete it in place to avoid ordering issues
            delete handlers[index];
            handlers.active--;
            if (handlers.active === 0){
                if (element[key]){
                    element[key] = null;
                    e.handlers[type] = [];
                }
            }
        }
        function eventStorm(element, type){
            return function(){
                var e = events[uid(element)];
                if(!e || !e.handlers[type]){
                    return;
                }
                var handlers = e.handlers[type],
                    len = handlers.length,
                    i;
                for(i = 0; i < len; i++){
                    // check the handler wasn't removed
                    if (handlers[i]){
                        handlers[i].apply(this, arguments);
                    }
                }
            };
        }
        // this is a fast way to identify our elements
        // .. at the expense of our memory, though.
        function uid(element){
            var index = map.indexOf(element);
            if (index === -1){
                map.push(element);
                index = map.length - 1;
            }
            return index;
        }
        window.events = {
            add: add,
            remove: remove
        };
    }(window); You can glance at how this can very quickly get out of hand. Remember this was just in the case of no W3C event model, and no IE event model. Fortunately, this is largely unnecessary nowadays. You can imagine how hacks of this kind are all over your favorite libraries. They have to be if they want to support the old, decrepit and outdated browsers. Some have been 

<a href="http://blog.jquery.com/2012/06/28/jquery-core-version-1-9-and-beyond/" rel="nofollow">taking steps back</a> from the support every single browser philosophy. I encourage you to read your favorite library's <a href="http://code.jquery.com/jquery.js" rel="nofollow">code</a>, and learn how they resolve these situations, or how they are written in general. 
## Event Delegation What the heck is event delegation? This is an ever ubiquitous interview question. Yet, every single time I'm asked this question during the course of an interview, the interviewers look surprised that I actually know what event delegation is. Other 

<a href="https://github.com/darcyclarke/Front-end-Developer-Interview-Questions" rel="nofollow">common interview questions</a> include event bubbling, event capturing, event propagation. Save the interview questions link in your <a href="http://getpocket.com/" rel="nofollow">pocket</a> and read it later to treat yourself to a little evaluation of your front-end development skills. It's good to know where you're standing. Event delegation is what you have to do when you have many elements which need the same event handler. It doesn't matter if the handler depends on the actual element, because event delegation accomodates that. Let's look at a use case. I'll use the <a href="http://jade-lang.com/" rel="nofollow">Jade</a> syntax. 
    body
        ul.foo
            li.bar
            li.bar
            li.bar
            li.bar
        ul.foo
            li.bar
            li.bar
            li.bar
            li.bar We want, for whatever reason, to attach an event handler to each .foo element. The problem is that event listening is resource consuming. It's lighter to attach a single event than thousands. Yet, it's surprisingly common to work in codebases with little to no event delegation. A better performing approach is to add a super event handler on a node which is a parent to every node that wants to listen to that event using this handler. And then: 

*   When the event is raised on one of the children, it <a href="http://www.quirksmode.org/js/events_order.html" rel="nofollow">bubbles up the DOM chain</a>
*   It reaches the parent node which has our super handler.
*   That special handler will check whether the <a href="https://developer.mozilla.org/en-US/docs/Web/API/event.target" rel="nofollow">event target</a> is one of the intended targets
*   Finally the actual handler will be invoked, passing it the appropriate event context. This is what happens when you bind events using jQuery code such as: 

    $('body').on('click', '.bar', function(){
        console.log('clicked bar!', $(this));
    }); As opposed to more unfortunate code: 

    $('.bar').on('click', function(){
        console.log('clicked bar!', $(this));
    }); Which would work pretty much the same way, except it will create one event handler for each .bar element, hindering performance. There is one crucial difference, however. Event handling done directly on a node works for just that node, forever. Event delegation works on any children that meet the criteria provided, .bar in this case. If you were to add more .bar elements to your DOM, those would also match the criteria, and therefore be attached to the super handler we created in the past. I won't be providing an example on raw JavaScript event delegation, but at least you now understand how it works and what it is, and hopefully, you understood why you need to use it. We've been mentioning selectors such as .bar this whole time, but how does that work? 

## Querying the DOM You might have heard of 

<a href="http://sizzlejs.com/" rel="nofollow">Sizzle</a>, the internal library jQuery uses as a selector engine. I'm not very knowledgeable on all of the internals of Sizzle, but if you are curious, <a href="https://github.com/jquery/sizzle/blob/master/dist/sizzle.js" rel="nofollow">take a look around</a> their codebase For the most part, it uses `c.querySelector` and `c.querySelectorAll`. These methods enjoy <a href="http://stackoverflow.com/questions/3856294/is-queryselector-supported-by-all-browsers" rel="nofollow">very good support accross browsers</a>. Sizzle performs optimizations such as picking whether to use `c.getElementById`, `c.getElementsByTagName`, `c.getElementsByClassName` or one of the <a href="https://developer.mozilla.org/en-US/docs/Web/API/Element.querySelector" rel="nofollow">querySelector</a> functions. It also fixes inconsistencies in IE8 as well as some other cross-browser fixes. Other than that, querying the DOM is pretty much done natively. Lets turn to <a href="http://api.jquery.com/category/manipulation/" rel="nofollow">manipulation</a>. 
## DOM Manipulation Manipulating the DOM is one of those things that is remarkably important to get right, and strikingly easy to get wrong. I think most people know how to add nodes to the DOM, so I won't waste my time on that. Instead, I'd like to talk about 

<a href="https://developer.mozilla.org/en-US/docs/Web/API/document.createDocumentFragment" rel="nofollow">createDocumentFragment</a>. `document.createDocumentFragment` allows us to create a DOM structure that's not attached to the main DOM tree. This allows us to create nodes that only exist in memory and helps us to <a href="http://www.stubbornella.org/content/2009/03/27/reflows-repaints-css-performance-making-your-javascript-slow/" rel="nofollow">avoid DOM reflowing</a>. Once our tree fragment is ready, we can attach it to the DOM. When we do, all the child nodes in the fragment are attached to the specified node. 
    var somewhere = document.getElementById('here'),
        fragment = document.createDocumentFragment(),
        i, foo;
    for(i = 0, i < 1000; i++){
        foo = document.createElement('div');
        foo.innerText = i;
        fragment.appendChild(foo);
    }
    somewhere.appendChild(fragment);

<a href="http://cdpn.io/sweoB" rel="nofollow">Pen here</a> There's a cute post on DocumentFragments, written by John Resig, you might want to <a href="http://ejohn.org/blog/dom-documentfragments/" rel="nofollow">check out</a>. 
## Conclusion Hopefully this post has given you some understanding of what is going on under the covers in some of the core aspects of popular libraries, like jQuery. This knowledge can be very useful not just when building your web application, but especially when debugging issues. 

*This article was originally published at <http://blog.ponyfoo.com/2013/06/10/uncovering-the-native-dom-api>*

 [1]: /authors/nicolas-bevacqua
 [2]: http://blog.ponyfoo.com/2013/05/08/taming-asynchronous-javascript