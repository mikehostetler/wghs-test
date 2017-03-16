---
ID: 353
post_title: LESS Tips and Tricks
author: admin
post_date: 2013-05-20 13:09:58
post_excerpt: ""
layout: post
permalink: >
  http://modern-web.dev/2013/05/20/less-tips-and-tricks/
published: true
dsq_thread_id:
  - "2809872611"
om_counter:
  - "186"
---
*By [Mária Jurčovičová][1]* [LESS][2] is declarative language designed to behave and look as closely to css as possible. It started as an attempt to make CSS style sheets easier to read and maintain and then added more features and abilities. It evolved so much that it became theoretically possible to do anything with it. This post shows some less known but very useful LESS features. It also contains a list of potentially useful workarounds and tricks you can use if the default LESS syntax is not sufficient for your specific needs. Finally, last section shows a practical application of these tricks, creating a mixin to auto-generate vendor prefixes for CSS declarations. 
## The Basics This article assumes that you know both what LESS is and how to use its basic features. If you know how LESS variables, mixins and nesting work, then you know enough to read this post. If you’ve never used them, you can start either with 

[official documentation][2] that belongs to original JavaScript compiler (less.js) or [detailed language description][3] written as part of Java compiler less4j. You can also read an [introductory article about LESS][4] written on my blog. 
## Compilers Just few weeks ago, we released the first non-beta version of a LESS compiler ported to Java called 

[less4j][5]. Feel free to try it out. The canonical compiler, the one whose behavior defines the language, is written in JavaScript and called [less.js][2]. It works both in the browser and in NodeJS. I’ve tested it on Windows using NodeJS (and npm) and found that it runs without problems. The next less.js version is 1.4.0 and will be backward incompatible, but for good reasons. It contains changes that are meant to solve some old quirks that I have covered, in part, in a [follow up post on my blog][6]. Of course, less4j will follow less.js changes as soon as possible. There are lists of [other ports][7] and [GUI editors][8] available on the LESS wiki page. 
## Lesser Known LESS Abilities This section will discuss four LESS  features. Three of them have been added only recently and one was documented only in the less.js issue database. The list of features covered by this section: 

*   mixins with return values;
*   semicolon as mixin arguments separator;
*   media query in variable;
*   import into ruleset or mixin.

**Mixins With Return Values** Variables declared in a mixin can act as return values. This is important not only because it can be occasionally useful, but also because this feature has one rather important "gotcha." Below is an example of a variables acting as a return values in a mixin: 
    .mixin() { @unique: 45; } /* mixin defines variable */
    
    #call-mixin {
      variable-value: @unique; /* print variable defined inside mixin */
      .mixin(); /* call the mixin */
    } All variables defined in mixin are copied into the caller’s scope and can rewrite local variables. In addition, any mixin can call other mixins and those other mixins can call yet other mixins. All that can create an arbitrary long chain of mixin calls. Any of them may modify the value of any variable defined within the original caller scope. The dangerous part, whereby a mixin variable can rewrite local variables, will be fixed in the next version, 1.4.0. The next release will copy only those variables that can not possibly rewrite local values. Consider following LESS: 

    .mixin() { @clash: 45; @unique: 45; } /* mixin defines two variables */
    
    #call-mixin {
      @clash: 0; /* variable with the same name as the one inside .mixin */
      /* print actual variables values */
      unique: @unique;
      clash: @clash;
      .mixin(); /* call the mixin */
    } Using version 1.3.3 it will compile to: 

    #call-mixin {
      unique: 45; /* variable defined inside mixin was accessible */
      clash: 45; /* variable defined inside mixin rewrote local value */
    } However compiled with LESS 1.4.0 (currently in beta): 

    #call-mixin {
      unique: 45; /* variable defined inside mixin was accessible */
      clash: 0; /* local variable kept its value*/
    } Note: LESS 1.4.0 will protect only local variables. Variables found in any other scope, for example the global scope, are not protected. Here is an example of how a non-local variable is not protected: 

    /* mixin with return value */
    .mixin() { @clash: 45; }
    
    /* variable with the same name .mixin return value */
    @clash: 0;
    
    #call-mixin {
      clash: @clash; /* print variables value */
      .mixin(); /* call the mixin */
    } The LESS 1.4.0 beta compilation result is: 

    #call-mixin {
      /* variable defined outside of local scope was not protected */
      clash: 45;
    }

**Semicolon as Mixin Arguments Separator** LESS 1.3.3 added the semicolon as mixin arguments separator. Both .call(1; 2; 3) and .call(1, 2, 3) are now valid mixin calls and do the same thing. A mixin call with semicolon separated arguments may look weird and unusual, but there was good reason for this addition. The comma has meaning in standard css. It is list separator and 1, 2, 3 is valid list containing three numbers. Using a comma as a mixin argument separator made it impossible to use comma separated lists as arguments. If the latest LESS compiler sees at least one semicolon inside mixin call or declaration, then it assumes that arguments are separated by semicolons and all commas belong to css lists: 
    /* two arguments, both with comma separated list */
    .call(1, 2, 3; something, else);
    /* three arguments and each contains one number */
    .call(1, 2, 3);
    /* one argument containing comma separated CSS list */
    .call(1, 2, 3;); // note dummy semicolon
    
    /*comma separated default value */
    .declaration(@param1: red, blue;) {
      // mixin content
    }

**Media Query in Variable** Recent releases of LESS have the ability to store media queries in variables and use those variables inside @media rules. This is useful whenever you need the same media query in multiple places. When you need to modify that media query, you will have only one place to change. In the below LESS example, we store and use a "tablet and (min-width: 500px)" media query: 
    /* note '~' in the beginning - media query must be escaped */
    @singleQuery: ~"tablet and (min-width: 500px)";
    @media screen, @singleQuery {
      set {
        padding: 3 3 3 3;
      }
    } This compiles into: 

    @media screen, tablet and (min-width: 500px) {
      set {
        padding: 3 3 3 3;
      }
    }

**Import** The LESS import statement is not limited to stylesheet’s top level. It is possible to import LESS files into rulesets and mixins. This feature helps to deal with name conflicts between multiple LESS libraries or LESS library versions. 
    .namespace-1-2-3() {
      @import "library-1.2.3.less";
    }
    .namespace-2-0-0() {
      @import "library-2.0.0.less";
    }
    #use-both-libraries-without-name-conflicts {
      .namespace-1-2-3 > .useful-mixin();
      .namespace-2-0-0 > .useful-mixin();
    }

## Tricks and Workarounds Some occasionally demanded but not yet directly supported features are achievable if you are willing to use a workaround. This section shows three such tricks: 

*   triple variable indirection;
*   interpolate property names;
*   create a loop.

**Triple Variable Indirection** Related less.js issue: [Variable Name Interpolation][9] Used LESS features: 
*   variables;
*   variable references;
*   string interpolation.

*What Works* LESS supports allowing any variable holding a string to act as reference to another variable. If you want to use a variable as a reference, you just have to put additional @ before the variable name (e.g., @@reference). Use the @reference variable as a reference to the @number variable: 
    @number: 10;
    @reference: "number";
    h1 { size: @@reference; } output: 

    h1 { size: 10; }

*What Does Not Work* LESS does not support triple variable indirection, meaning @@@referenceToReference is not valid LESS: 
    @number: 10;
    @reference: "number";
    @referenceToReference: "reference"; // desperate attempt
    h1 { size: @@@referenceToReference; } //syntax error

*Workaround* You can use string interpolation to compose a referenced variable name: 
    @number: 10;
    @reference: "number";
    /* compose indirect variable name inside another variable */
    @referenceToReference: "@{reference}";
    h1 { size: @@referenceToReference; } The previous LESS code compiles into following CSS: 

    h1 { size: 10; }

*Bonus* Using string interpolation allows you to create references of technically unlimited depth: 
    @number: 10;
    @reference: "number";
    @referenceToReference: "@{reference}";
    /* chaining of interpolated variables can be arbitrary long */
    @referenceToReference2: "@{referenceToReference}";
    @referenceToReference3: "@{referenceToReference2}";
    @referenceToReference4: "@{referenceToReference3}";
    @referenceToReference5: "@{referenceToReference4}";
    h1 { size: @@referenceToReference5; }

**Interpolate Property Names** Related less.js issue: [Auto Prefix replacement][10] Used LESS features: 
*   escaped values,
*   variables.   

*What Works* Any content can be stored into a variable and then used 
*   as property value;
*   inside string or escaped value;
*   as selector;
*   as media query. For example, take the following LESS: 

    @property_value: 1px;
    @selector: ~"div.someclass";
    @media_query: ~"tablet";
    
    @media @media_query {
      @{selector} {
        padding: @property_value;
        in-string: "Padding was set to: @{property_value}";
      }
    } This compiles into: 

    @media tablet {
      div.someclass {
        padding: 1px;
        in-string: "Padding was set to: 1px";
      }
    }

*What Does Not Work* Property names cannot be rendered dynamically. You cannot use variables or escaped values to generate property names: 
    @css-property-name: "size";
    #desperate-1 {
      @css-property-name: 10; // syntax error
    } 
    #desperate-2 {
      @{css-property-name}: 10; // syntax error
    }

*Workaround* Browsers ignore declarations with an unknown property. For example the declaration "hack: 1;" will be ignored by all browsers, so whether or not the generated css contains it will have no impact. In addition, LESS escaping allows you to place any content directly into generated CSS. Escaped values use an ~"escaped value" syntax and the intention was to allow you to use browser hacks, invalid css or proprietary syntax that would otherwise be unrecognized by LESS. Regardless of original intentions, this hack: 1 ~"; something" will be rendered as hack: 1 ; something, so we can abuse it. For example, here we use this to render a property name from a variable: 
    @css-property-name: "size";
    #workaround {
      hack: 1 ~"; @{css-property-name}:" 10;
    } The previous LESS code compiles into following CSS: 

    #workaround {
      hack: 1 ; size: 10;
    }

**Looping** Related less.js issue: [Idea: loop support][11] Used LESS features: 
*   mixins;
*   guards;
*   expressions.

*What Does Not Work* LESS is declarative language and therefore does not support looping. It does not have native while, for, repeat ... until or any other type of cycling structure. *Workaround* Recursive mixins calls can simulate the loop. The most simple looping mixin needs only one parameter - how many times should it run the loop. It does whatever it needs to do and then calls itself with the smaller parameter. The "looping" stops once the parameter reaches zero. Any cycling structure can be simulated in this or a similar manner, but you have to be careful. It is very easy to write endlessly running mixin. In the following LESS code, we create a looping mixin: 
    /* looping mixin */
    .loop (@index) when (@index > 0) {
      loop: member @index; /* do whatever you need to do */
      .loop((@index - 1)); /* loop again */
    }
    /* stop mixin */
    .loop (@index) when (@index <= 0) {
      loop: end;
    }
    
    /* use looping mixin */
    #run-loop-5-times {
      .loop(5);
    } This compiles into: 

    #run-loop-5-times {
      loop: member 5;
      loop: member 4;
      loop: member 3;
      loop: member 2;
      loop: member 1;
      loop: end;
    } Note: we used double parentheses in the .loop((@index - 1)) call. This was intentional and not a mistake. LESS 2.0.0 will require parentheses around math operations, so we added them to future-proof our example. 

*Who Would Use That?* [Twitter Bootstrap][12] uses looping to generate [the grid][13] as you can see in the following simplified version of the Bootstrap code: 
    /* Simplified Twitter Bootstrap Code */
    .core (@gridColumns, @gridColumnWidth) {
    
      .spanX (@index) when (@index > 0) { /* looping mixin */
        (~".span@{index}") { .span(@index); } /* generate column */
        .spanX(@index - 1); /* loop again */
      }
      .spanX (0) {} /* stop mixin */
    
      .span (@columnIndx) {
        width: (@gridColumnWidth * @columnIndx);
      }
    
      // generate .spanX and .offsetX
      .spanX (@gridColumns);
    }
    
    /* generate the grid */
    .sample-grid {
      @gridColumns: 3;
      @gridColumnWidth: 55;
      .core(@gridColumns, @gridColumnWidth);
    } This compiles into: 

    .sample-grid .span3 {
      width: 165;
    }
    .sample-grid .span2 {
      width: 110;
    }
    .sample-grid .span1 {
      width: 55;
    }

*Example: Loop Through a List* A mixin to loop through a list of strings or escaped values does not end when the index reaches zero, but rather checks the type of the next list member instead to determine the end of the list: 
    .loop-strings(@list, @index: 1) when (isstring(extract(@list, @index))) {
      @currentMember: extract(@list, @index);
      do-something-with: @currentMember;
      .loop-strings(@list, (@index + 1)); /* loop the next member */
    }
    
    #use-place {
      // loop through list of escaped values
      .loop-strings(~"A", ~"B", ~"C", ~"1", ~"2", ~"3";);
    } compiles into: 

    #use-place {
      do-something-with: A;
      do-something-with: B;
      do-something-with: C;
      do-something-with: 1;
      do-something-with: 2;
      do-something-with: 3;
    }

## Auto-Generate Browser Vendor Prefixes Auto-generation of the standard browser vendor prefixes in CSS declarations is one of those features that are repeatedly asked for within the less.js issues database. While LESS currently does not have this feature, it is possible to combine the workarounds we’ve discussed into a mixin that accomplishes the task. 

**API** The mixin .vendorPrefixes(@prefixes, @declaration, @values...) has two mandatory arguments followed by any number of optional arguments: 
*   @prefixes - a mandatory list of prefixes,
*   @declaration - a mandatory string with the declared property,
*   @values... - optional - all declared values. This solution is not perfect, the declaration to be rendered has to be written inside a string and sent as a mixin parameter: 

    .vendorPrefixes(@list-of-prefixes; "transition"; all 4s ease); That mixn call certainly has a less than ideal readability. On the other hand, this solution shows the full power of LESS and combines two different tricks into one solution, so it is still useful for demonstration purposes. 

**How It Works** The rendering mixin uses the interpolate property names trick to render a vendor prefixed declaration: 
    /* Render exactly vendor prefixed declaration. */
    .renderWithPrefix(@prefix; @declaration; @values...) {
      hack: 1 ~"; @{prefix}-@{declaration}: " @values;
    } The looping mixin goes through all prefixes: 

    .nthVendorPrefix(@prefixes; @index; @declaration; @values...)
    when (isstring(extract(@prefixes, @index))) {
      @prefix: extract(@prefixes, @index);
      .renderWithPrefix(@prefix, @declaration, @values);
      .nthVendorPrefix(@prefixes; (@index + 1); @declaration; @values);
    } Finally, the "API" mixin starts the looping and rendering process: 

    .vendorPrefixes(@prefixes, @declaration, @values...) {
      .nthVendorPrefix(@prefixes; 1; @declaration; @values);
    } Here is the full LESS code: 

    /* Render exactly vendor prefixed declaration. */
    .renderWithPrefix(@prefix; @declaration; @values...) {
      hack: 1 ~"; @{prefix}-@{declaration}: " @values;
    }
    
    /* Looping mixin assumes that @prefixes list contains strings:
       * finds @index-th prefix in @prefixes list,
       * renders one vendor prefixed declaration,
       * loop: call itself with higher @index.
    
      Guard prevents indefinite looping, it will stop once @index goes off the list.
    */
    .nthVendorPrefix(@prefixes; @index; @declaration; @values...)
    when (isstring(extract(@prefixes, @index))) {
      @prefix: extract(@prefixes, @index);
      .renderWithPrefix(@prefix, @declaration, @values);
      .nthVendorPrefix(@prefixes; (@index + 1); @declaration; @values);
    }
    
    /* Render exactly vendor prefixed declaration. */
    .vendorPrefixes(@prefixes, @declaration, @values...) {
      .nthVendorPrefix(@prefixes; 1; @declaration; @values);
    }
    
    #use-place {
      @list-of-prefixes: ~"-moz", ~"-ie", ~"-webkit";
      .vendorPrefixes(@list-of-prefixes; "transition"; all 4s ease);
    } compiles into: 

    #use-place {
      hack: 1 ; -moz-transition:  all 4s ease;
      hack: 1 ; -ie-transition:  all 4s ease;
      hack: 1 ; -webkit-transition:  all 4s ease;
    }

## Where To Go From Here As mentioned earlier, I wrote a 

[follow-up post][6] that covers LESS expressions and their planned backward incompatible changes in 1.4.0. In most cases, expressions are easy to use and very useful. Unfortunately, their interactions with the rest of the CSS syntax can occasionally cause somewhat quirky behavior. [The post][6] shows when this happens, what will be solved by the planned changes and which traps will still remain. *This article was originally posted at <http://meri-stuff.blogspot.sk/2013/03/less-css-tips-and-trics.html>*

 [1]: /authors/maria-jurcovicova
 [2]: http://lesscss.org/
 [3]: https://github.com/SomMeri/less4j/wiki/Supported-Less-Language
 [4]: http://meri-stuff.blogspot.sk/2012/06/inheritance-and-variables-in-css-with.html
 [5]: https://github.com/SomMeri/less4j#readme
 [6]: http://meri-stuff.blogspot.sk/2013/05/less-css-expressions-and-traps.html
 [7]: https://github.com/cloudhead/less.js/wiki/Ports-of-LESS
 [8]: https://github.com/cloudhead/less.js/wiki/Editors-and-Plugins
 [9]: https://github.com/cloudhead/less.js/issues/1188
 [10]: https://github.com/cloudhead/less.js/issues/1199
 [11]: https://github.com/cloudhead/less.js/issues/869
 [12]: http://twitter.github.com/bootstrap/
 [13]: http://twitter.github.com/bootstrap/scaffolding.html#gridSystem