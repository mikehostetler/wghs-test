---
ID: 63
post_title: >
  45 Useful JavaScript Tips, Tricks and
  Best Practices
author: admin
post_date: 2016-11-18 17:08:16
post_excerpt: ""
layout: post
permalink: >
  http://modern-web.dev/2016/11/18/45-useful-javascript-tips-tricks-and-best-practices/
published: true
featured_author:
  - 'No'
  - 'No'
---
<em>By <a href="/authors/saad-mousliki">Saad Mousliki</a></em>

As you know, JavaScript is the number one programming language in the world, the language of the web, of mobile hybrid apps (like <a href="http://phonegap.com/">PhoneGap</a> or <a href="http://www.appcelerator.com/">Appcelerator</a>), of the server side (like <a href="http://nodejs.org">NodeJS</a> or <a href="http://wakanda.org">Wakanda</a>) and has many other implementations. It's also the starting point for many new developers to the world of programming, as it can be used to display a simple alert in the web browser but also to control a robot (using <a href="http://nodebots.io/">nodebot</a>, or <a href="http://semu.github.io/noduino/">nodruino</a>). The developers who master JavaScript and write organized and performant code have become the most sought after in the job market.

In this article, I'll share a set of JavaScript tips, tricks and best practices that should be known by all JavaScript developers regardless of their browser/engine or the SSJS (Server Side JavaScript) interpreter.

Note that the code snippets in this article have been tested in the latest Google Chrome version 30, which uses the V8 JavaScript Engine (V8 3.20.17.15).

<strong>1 - Don’t forget <code>var</code> keyword when assigning a variable’s value for the first time.</strong>

Assignment to an undeclared variable automatically results in a global variable being created. Avoid global variables.

<strong>2 - use <code>===</code> instead of <code>==</code></strong>

The <code>==</code> (or <code>!=</code>) operator performs an automatic type conversion if needed. The <code>===</code> (or <code>!==</code>) operator will not perform any conversion. It compares the value and the type, which could be considered faster than <code>==</code>.
<pre><code>[10] === 10    // is false
[10]  == 10    // is true
'10' == 10     // is true
'10' === 10    // is false
 []   == 0     // is true
 [] ===  0     // is false
 '' == false   // is true but true == "a" is false
 '' ===   false // is false </code></pre>
<strong>3 - <code>undefined</code>, <code>null</code>, 0, <code>false</code>, <code>NaN</code>, <code>''</code> (empty string) are all falsy.</strong>
<strong>4 - Use Semicolons for line termination</strong>

The use of semi-colons for line termination is a good practice. You won't be warned if you forget it, because in most cases it will be inserted by the JavaScript parser. For more details about why you should use semi-colons, take a look to this artice: <a href="http://davidwalsh.name/javascript-semicolons">http://davidwalsh.name/javascript-semicolons</a>.

<strong>5 - Create an object constructor</strong>
<pre><code>function Person(firstName, lastName){
    this.firstName =  firstName;
    this.lastName = lastName;        
}  

var Saad = new Person("Saad", "Mousliki");</code></pre>
<strong>6 - Be careful when using <code>typeof</code>, <code>instanceof</code> and <code>constructor</code>.</strong>
<ul>
	<li><em>typeof</em> : a JavaScript unary operator used to  return a string that represents the primitive type of a variable,  don't forget that <code>typeof null</code> will return "object", and for the majority of object types (Array, Date, and others) will return also "object".</li>
	<li><em>constructor</em> : is a property of the internal prototype property, which could be overridden by code.</li>
	<li><em>instanceof</em> : is another JavaScript operator that check in all the prototypes chain the constructor it returns true if it's found and false if not.</li>
</ul>
<pre><code>var arr = ["a", "b", "c"];
typeof arr;   // return "object" 
arr  instanceof Array // true
arr.constructor();  //[]
</code></pre>
<strong>7 - Create a Self-calling Function</strong>

This is often called a Self-Invoked Anonymous Function or Immediately Invoked Function Expression (IIFE). It is a function that executes automatically when you create it, and has the following form:
<pre><code>(function(){
    // some private code that will be executed automatically
})();  
(function(a,b){
    var result = a+b;
    return result;
})(10,20)</code></pre>
<strong>8 - Get a random item from an array</strong>
<pre><code>var items = [12, 548 , 'a' , 2 , 5478 , 'foo' , 8852, , 'Doe' , 2145 , 119];

var  randomItem = items[Math.floor(Math.random() * items.length)];</code></pre>
<strong>9 - Get a random number in a specific range</strong>

This code snippet can be useful when trying to generate fake data for testing purposes, such as a salary between min and max.
<pre><code>var x = Math.floor(Math.random() * (max - min + 1)) + min;</code></pre>
<strong>10 - Generate an array of numbers with numbers from 0 to max</strong>
<pre><code>var numbersArray = [] , max = 100;

for( var i=1; numbersArray.push(i++) &lt; max;);  // numbers = [1,2,3 ... 100] </code></pre>
<strong>11 - Generate a random set of alphanumeric characters</strong>
<pre><code>function generateRandomAlphaNum(len) {
    var rdmString = "";
    for( ; rdmString.length &lt; len; rdmString  += Math.random().toString(36).substr(2));
    return  rdmString.substr(0, len);

}
</code></pre>
<strong>12 - Shuffle an array of numbers</strong>
<pre><code>var numbers = [5, 458 , 120 , -215 , 228 , 400 , 122205, -85411];
numbers = numbers.sort(function(){ return Math.random() - 0.5});
/* the array numbers will be equal for example to [120, 5, 228, -215, 400, 458, -85411, 122205]  */</code></pre>
A better option could be to implement a random sort order by code (e.g. : Fisher-Yates shuffle), than using the native sort JavaScript function. For more details take a look to <a href="http://stackoverflow.com/questions/962802/is-it-correct-to-use-javascript-array-sort-method-for-shuffling/962890#962890">this discussion</a>.

<strong>13 - A string trim function</strong>

The classic trim function of Java, C#, PHP and many other language that remove whitespace from a string doesn’t exist in JavaScript, so we could add it to the <code>String</code> object.
<pre><code>String.prototype.trim = function(){return this.replace(/^s+|s+$/g, "");};  </code></pre>
A native implementation of the trim() function is available in the recent JavaScript engines.

<strong>14 - Append an array to another array</strong>
<pre><code>var array1 = [12 , "foo" , {name "Joe"} , -2458];

var array2 = ["Doe" , 555 , 100];
Array.prototype.push.apply(array1, array2);
/* array1 will be equal to  [12 , "foo" , {name "Joe"} , -2458 , "Doe" , 555 , 100] */</code></pre>
<strong>15 - Transform the <code>arguments</code> object into an array</strong>
<pre><code>var argArray = Array.prototype.slice.call(arguments);</code></pre>
<strong>16 - Verify that a given argument is a number</strong>
<pre><code>function isNumber(n){
    return !isNaN(parseFloat(n)) &amp;&amp; isFinite(n);
}</code></pre>
<strong>17 - Verify that a given argument is an array</strong>
<pre><code>function isArray(obj){
    return Object.prototype.toString.call(obj) === '[object Array]' ;
}</code></pre>
Note that if the toString() method is overridden, you will not get the expected result using this trick.

Or use...
<pre><code>Array.isArray(obj); // its a new Array method</code></pre>
You could also use <code>instanceof</code> if you are not working with multiple frames. However, if you have many contexts, you will get a wrong result.
<pre><code>var myFrame = document.createElement('iframe');
document.body.appendChild(myFrame);

var myArray = window.frames[window.frames.length-1].Array;
var arr = new myArray(a,b,10); // [a,b,10]  

// instanceof will not work correctly, myArray loses his constructor 
// constructor is not shared between frames
arr instanceof Array; // false</code></pre>
<strong>18 - Get the max or the min in an array of numbers</strong>
<pre><code>var  numbers = [5, 458 , 120 , -215 , 228 , 400 , 122205, -85411]; 
var maxInNumbers = Math.max.apply(Math, numbers); 
var minInNumbers = Math.min.apply(Math, numbers);</code></pre>
<strong>19 - Empty an array</strong>
<pre><code>var myArray = [12 , 222 , 1000 ];  
myArray.length = 0; // myArray will be equal to [].</code></pre>
<strong>20 - Don’t use delete to remove an item from array</strong>

Use <code>splice</code> instead of using <code>delete</code> to delete an item from an array. Using <code>delete</code> replaces the item with <code>undefined</code> instead of the removing it from the array.

Instead of...
<pre><code>var items = [12, 548 ,'a' , 2 , 5478 , 'foo' , 8852, , 'Doe' ,2154 , 119 ]; 
items.length; // return 11 
delete items[3]; // return true 
items.length; // return 11 
/* items will be equal to [12, 548, "a", undefined × 1, 5478, "foo", 8852, undefined × 1, "Doe", 2154,       119]   */</code></pre>
Use...
<pre><code>var items = [12, 548 ,'a' , 2 , 5478 , 'foo' , 8852, , 'Doe' ,2154 , 119 ]; 
items.length; // return 11 
items.splice(3,1) ; 
items.length; // return 10 
/* items will be equal to [12, 548, "a", 5478, "foo", 8852, undefined × 1, "Doe", 2154,       119]   */</code></pre>
The delete method should be used to delete an object property.

<strong>21 - Truncate an array using length</strong>

Like the previous example of emptying an array, we truncate it using the <code>length</code> property.
<pre><code>var myArray = [12 , 222 , 1000 , 124 , 98 , 10 ];  
myArray.length = 4; // myArray will be equal to [12 , 222 , 1000 , 124].</code></pre>
As a bonus, if you set the array length to a higher value, the length will be changed and new items will be added with <code>undefined</code> as a value. The array length is not a read only property.
<pre><code>myArray.length = 10; // the new array length is 10 
myArray[myArray.length - 1] ; // undefined</code></pre>
<strong>22 - Use logical AND/ OR for conditions</strong>
<pre><code>var foo = 10;  
foo == 10 &amp;&amp; doSomething(); // is the same thing as if (foo == 10) doSomething(); 
foo == 5 || doSomething(); // is the same thing as if (foo != 5) doSomething();</code></pre>
The logical OR could also be used to set a default value for function argument.
<pre><code>function doSomething(arg1){ 
    arg1 = arg1 || 10; // arg1 will have 10 as a default value if it’s not already set
}</code></pre>
<strong>23 - Use the map() function method to loop through an array's items</strong>
<pre><code>var squares = [1,2,3,4].map(function (val) {  
    return val * val;  
}); 
// squares will be equal to [1, 4, 9, 16] </code></pre>
<strong>24 - Rounding number to N decimal place</strong>
<pre><code>var num =2.443242342;
num = num.toFixed(4);  // num will be equal to 2.4432</code></pre>
NOTE : the <code>toFixed()</code> function returns a string and not a number.

<strong>25 - Floating point problems</strong>
<pre><code>0.1 + 0.2 === 0.3 // is false 
9007199254740992 + 1 // is equal to 9007199254740992  
9007199254740992 + 2 // is equal to 9007199254740994</code></pre>
Why does this happen? 0.1 +0.2 is equal to 0.30000000000000004. What you need to know is that all JavaScript numbers are floating points represented internally in 64 bit binary according to the IEEE 754 standard. For more explanation, take a look to <a href="http://www.2ality.com/2012/04/number-encoding.html">this blog post</a>.

You can use <code>toFixed()</code> and <code>toPrecision()</code> to resolve this problem.

<strong>26 - Check the properties of an object when using a for-in loop</strong>

This code snippet could be useful in order to avoid iterating through the properties from the object’s prototype.
<pre><code>for (var name in object) {  
    if (object.hasOwnProperty(name)) { 
        // do something with name                    
    }  
}</code></pre>
<strong>27 - Comma operator</strong>
<pre><code>var a = 0; 
var b = ( a++, 99 ); 
console.log(a);  // a will be equal to 1 
console.log(b);  // b is equal to 99</code></pre>
<strong>28 - Cache variables that need calculation or querying</strong>

In the case of a jQuery selector, we could cache the DOM element.
<pre><code>var navright = document.querySelector('#right'); 
var navleft = document.querySelector('#left'); 
var navup = document.querySelector('#up'); 
var navdown = document.querySelector('#down');</code></pre>
<strong>29 - Verify the argument before passing it to <code>isFinite()</code></strong>
<pre><code>isFinite(0/0) ; // false 
isFinite("foo"); // false 
isFinite("10"); // true 
isFinite(10);   // true 
isFinite(undefined);  // false 
isFinite();   // false 
isFinite(null);  // true  !!! </code></pre>
<strong>30 - Avoid negative indexes in arrays</strong>
<pre><code>var numbersArray = [1,2,3,4,5]; 
var from = numbersArray.indexOf("foo") ;  // from is equal to -1 
numbersArray.splice(from,2);    // will return [5]</code></pre>
Make sure that the arguments passed to <code>splice</code> are not negative.

<strong>31 - Serialization and deserialization (working with JSON)</strong>
<pre><code>var person = {name :'Saad', age : 26, department : {ID : 15, name : "R&amp;D"} }; 
var stringFromPerson = JSON.stringify(person); 
/* stringFromPerson is equal to "{"name":"Saad","age":26,"department":{"ID":15,"name":"R&amp;D"}}"   */ 
var personFromString = JSON.parse(stringFromPerson);  
/* personFromString is equal to person object  */</code></pre>
<strong>32 - Avoid the use of <code>eval()</code> or the <code>Function</code> constructor</strong>

Use of <code>eval</code> or the <code>Function</code> constructor are expensive operations as each time they are called script engine must convert source code to executable code.
<pre><code>var func1 = new Function(functionCode);
var func2 = eval(functionCode);</code></pre>
<strong>33 - Avoid using <code>with()</code> (The good part)</strong>

Using <code>with()</code> inserts a variable at the global scope. Thus, if another variable has the same name it could cause confusion and overwrite the value.

<strong>34 - Avoid using for-in loop for arrays</strong>

Instead of using...
<pre><code>var sum = 0;  
for (var i in arrayNumbers) {  
    sum += arrayNumbers[i];  
}</code></pre>
...it’s better to use...
<pre><code>var sum = 0;  
for (var i = 0, len = arrayNumbers.length; i &lt; len; i++) {  
    sum += arrayNumbers[i];  
}</code></pre>
As a bonus, the instantiation of <code>i</code> and <code>len</code> is executed once because it’s in the first statement of the for loop. Thsi is faster than using...
<pre><code>for (var i = 0; i &lt; arrayNumbers.length; i++)</code></pre>
Why? The length of the array <code>arrayNumbers</code> is recalculated every time the loop iterates.

NOTE : the issue of recalculating the length in each iteration was fixed in the latest JavaScript engines.

<strong>35 - Pass functions, not strings, to <code>setTimeout()</code> and <code>setInterval()</code></strong>

If you pass a string into <code>setTimeout()</code> or <code>setInterval()</code>, the string will be evaluated the same way as with <code>eval</code>, which is slow. Instead of using...
<pre><code>setInterval('doSomethingPeriodically()', 1000);  
setTimeout('doSomethingAfterFiveSeconds()', 5000);</code></pre>
...use...
<pre><code>setInterval(doSomethingPeriodically, 1000);  
setTimeout(doSomethingAfterFiveSeconds, 5000);</code></pre>
<strong>36 - Use a switch/case statement instead of a series of if/else</strong>

Using switch/case is faster when there are more than 2 cases, and it is more elegant (better organized code). Avoid using it when you have more than 10 cases.

<strong>37 - Use switch/case statement with numeric ranges</strong>

Using a switch/case statement with numeric ranges is possible with this trick.
<pre><code>function getCategory(age) {  
    var category = "";  
    switch (true) {  
        case isNaN(age):  
            category = "not an age";  
            break;  
        case (age &gt;= 50):  
            category = "Old";  
            break;  
        case (age &lt;= 20):  
            category = "Baby";  
            break;  
        default:  
            category = "Young";  
            break;  
    };  
    return category;  
}  
getCategory(5);  // will return "Baby"</code></pre>
<strong>38 - Create an object whose prototype is a given object</strong>

It’s possible to write a function that creates an object whose prototype is the given argument like this...
<pre><code>function clone(object) {  
    function OneShotConstructor(){}; 
    OneShotConstructor.prototype= object;  
    return new OneShotConstructor(); 
} 
clone(Array).prototype ;  // []</code></pre>
<strong>39 - An HTML escaper function</strong>
<pre><code>function escapeHTML(text) {  
    var replacements= {"&lt;": "&amp;lt;", "&gt;": "&amp;gt;","&amp;": "&amp;amp;", """: "&amp;quot;"};                      
    return text.replace(/[&lt;&gt;&amp;"]/g, function(character) {  
        return replacements[character];  
    }); 
}</code></pre>
<strong>40 - Avoid using try-catch-finally inside a loop</strong>

The try-catch-finally construct creates a new variable in the current scope at runtime each time the catch clause is executed where the caught exception object is assigned to a variable.

Instead of using...
<pre><code>var object = ['foo', 'bar'], i;  
for (i = 0, len = object.length; i &lt;len; i++) {  
    try {  
        // do something that throws an exception 
    }  
    catch (e) {   
        // handle exception  
    } 
}</code></pre>
...use...
<pre><code>var object = ['foo', 'bar'], i;  
try { 
    for (i = 0, len = object.length; i &lt;len; i++) {  
        // do something that throws an exception 
    } 
} 
catch (e) {   
    // handle exception  
} </code></pre>
<strong>41 - Set timeouts to <code>XMLHttpRequests</code></strong>

You could abort the connection if an XHR takes a long time (for example, due to a network issue), by using <code>setTimeout()</code> with the XHR call.
<pre><code>var xhr = new XMLHttpRequest (); 
xhr.onreadystatechange = function () {  
    if (this.readyState == 4) {  
        clearTimeout(timeout);  
        // do something with response data 
    }  
}  
var timeout = setTimeout( function () {  
    xhr.abort(); // call error callback  
}, 60*1000 /* timeout after a minute */ ); 
xhr.open('GET', url, true);  

xhr.send();</code></pre>
As a bonus, you should generally avoid synchronous XHR calls completely.

<strong>42 - Deal with WebSocket timeout</strong>

Generally when a WebSocket connection is established, a server could time out your connection after 30 seconds of inactivity. The firewall could also time out the connection after a period of inactivity.

To deal with the timeout issue you could send an empty message to the server periodically. To do this, add these two functions to your code: one to keep alive the connection and the other one to cancel the keep alive. Using this trick, you’ll control the timeout.

Add a <code>timerID</code>...
<pre><code>var timerID = 0; 
function keepAlive() { 
    var timeout = 15000;  
    if (webSocket.readyState == webSocket.OPEN) {  
        webSocket.send('');  
    }  
    timerId = setTimeout(keepAlive, timeout);  
}  
function cancelKeepAlive() {  
    if (timerId) {  
        cancelTimeout(timerId);  
    }  
}</code></pre>
The <code>keepAlive()</code> function should be added at the end of the <code>onOpen()</code> method of the webSocket connection and the <code>cancelKeepAlive()</code> at the end of the <code>onClose()</code> method.

<strong>43 - Keep in mind that <a href="http://dev.opera.com/articles/view/efficient-javascript/?page=2#primitiveoperator">primitive operations can be faster than function calls</a>. Use <a href="http://vanilla-js.com/">VanillaJS</a>.</strong>

For example, instead of using...
<pre><code>var min = Math.min(a,b); 
A.push(v);</code></pre>
...use...
<pre><code>var min = a &lt; b ? a : b; 
A[A.length] = v;</code></pre>
<strong>44 - Don’t forget to use a code beautifier when coding. Use JSLint and minification (JSMin, for example) before going live.</strong>

<strong>45 - JavaScript is awesome: <a href="http://stackoverflow.com/questions/11246/best-resources-to-learn-javascript">Best Resources To Learn JavaScript</a></strong>
<ul>
	<li>Code Academy JavaScript tracks: <a href="http://www.codecademy.com/tracks/javascript">http://www.codecademy.com/tracks/javascript</a></li>
	<li>Eloquent JavaScript by Marjin Haverbeke: <a href="http://eloquentjavascript.net/">http://eloquentjavascript.net/</a></li>
	<li>Advanced JavaScript by John Resig: <a href="http://ejohn.org/apps/learn/">http://ejohn.org/apps/learn/</a></li>
</ul>
<h2>Conclusion</h2>
I know that there are many other tips, tricks and best practices, so if you have any ones to add or if you have any feedback or corrections to the ones that I have shared, please adda comment.
<h2>References</h2>
In this article I have used my own code snippets. Some of the snippets are inspired from other articles and forums:
<ul>
	<li><a href="http://developer.nokia.com/Community/Wiki/JavaScript_Performance_Best_Practices">JavaScript Performance Best Practices</a> (<a href="http://creativecommons.org/licenses/by-nc-sa/2.5/">CC</a>)</li>
	<li><a href="https://code.google.com/p/jslibs/wiki/JavascriptTips">Google Code JavaScript tips</a></li>
	<li><a href="http://stackoverflow.com/questions/724826/javascript-tips-and-tricks-javascript-best-practices">StackOverFlow tips and tricks</a></li>
	<li><a href="http://stackoverflow.com/questions/6888409/settimeout-for-xhr-requests">TimeOut for XHR</a></li>
</ul>