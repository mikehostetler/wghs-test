---
ID: 1126
post_title: TypeScript 0.9 – What’s Improved
author: admin
post_date: 2013-10-21 11:21:11
post_excerpt: ""
layout: post
permalink: >
  http://modern-web.dev/2013/10/21/typescript-0-9-whats-improved/
published: true
dsq_thread_id:
  - "2809890397"
om_counter:
  - "4"
---
*By [Nicholas Wolverson][1]*</p> I've been noodling around with [Typescript][2] for a while and enjoying the various improvements over standard Javascript. Quite a bit of this comes from ES6 improvements which Typescript incorporates (and transforms to a sensible ES3 output) - for example real classes, and `this`-capturing lambda expressions that help avoid the number one kick-yourself error of Javascript stupidity. Syntactic sugar aside, there are a few reasons I like Typescript. An improved IDE experience in VS (and VS2012 is already quite good with intellisense via jsdoc comments and JS execution). Static typing improves the self-documenting nature of code, helps enforce a cleaner design, and ensures program correctness. Type definition files are available (for example, the [DefinitelyTyped][3] project maintains a good selection) for popular JS libraries to allow their use with full type information in a Typescript program, and this interoperability is huge. In this post I'm going to discuss some of the important improvements in the recent TypeScript [0\.9][4] and [0\.9.1][5] releases. 
## noImplicitAny {#noimplicitany} Javascript has some weird and wonderful features which make layering static typing over it somewhat hard. On top of the oddities that no sane programmer would expect, the dynamic nature of the language make some things hard to statically type. There are 2 ways to approach features which cannot be typed in an existing version of the type system, one is to refrain from using them in order to improve your program's maintainability, ease of understanding, correctness, etc (the lint-style approach of using "the good parts"). The other is to enhance the type system to be more expressive. Typescript's 

`any` type covers all the cases where the type of an expression can be basically anything at runtime. This allows for the awkward cases where the type system is not powerful enough to express what the programmer knows - an escape clause. It also functions as the type where standard Javascript code is present without type annotations (which are optional): 
    function f(n, m) {
        return n + m; // has type 'any'
    }
    function g(n: number, m: number) {
        return n + m; // has type 'number'
    } When developing Typescript code with the intention of using full type annotations and achieving type safety, this implicit 'any' typing can be more of a nuisance than a help. In fact, since Typescript has type inference, this becomes rather hard to spot - you don't 

*need* type annotations everywhere, just wherever the type system is not able to infer the correct type. But you might assume that a type is inferred where in fact it is just implicitly 'any', and then you have the perception of type safety without any of the guarantees. Typescript 0.9.1 introduces the `--noImplicitAny` flag to disallow variables where the inferred type is any. With this flag, the above program will not compile: 
    any.ts(23,12): error TS7006: Parameter 'n' of 'f' implicitly has an 'any' type.
    any.ts(23,15): error TS7006: Parameter 'm' of 'f' implicitly has an 'any' type.

## Generics {#generics} Functional languages have had the notion of "polymorphism" for a long time. This is when data structures and functions can operate over any type. Often this is used with lists. For example, this F# function works to repeat an element of any type into a list of repetitions of that item: 

    > let rec repeat n x = [ for m in 1..n -> x]
      let a = repeat 3 "a"
      let b = repeat 5 5
    
    val repeat : n:int -> x:'a -> 'a list
    val a : string list = ["a"; "a"; "a"]
    val b : int list = [5; 5; 5; 5; 5] Here 

`'a` is a type variable, standing for any type (and `'a list` is a list of things of that type). This is in the F# core as `List.replicate`. In contrast to this "parametric polymorphism", object oriented languages have long provided a different feature of "subtype polymorphism". This is classically useful to treat your button and textbox alike as widgets to compose your UI (and many other things). One obvious thing that this does not help with is the case of homogeneous collections, and so the mainstream OO languages (C++, Java, C#) now (eventually!) all have a form of "generics" which allow us to express strongly typed operations over arbitrary types ("generics" and "[parametric] polymorphism" are terminology for the same thing from different worlds). For example, the above in C#: 
    static IEnumerable<T> Repeat<T>(T x, int n) {
        for (i = 0; i < n; i++) {
            yield return x;
        }
    } (this is a naive version of LINQ's 

`Enumerable.Repeat`). Now to Typescript. As Javascript does not have static typing, one can easily write functions which are "morally generic". Here's a definition of a similar repeat function in Typescript: 
    function repeat(n: number, x: any) {
        var l : any[] = [];
        for (var i = 0; i < n; i++)
        {
            l.push(x);
        }
        return l;
    } Note that without knowing the type of 

`x`, we've assigned it the `any` type, and as a result the function returns `any[]` (if we omit type annotations on `x` and `l` they would be implicitly `any` to the same result). Typescript 0.9 introduces a generics syntax which would change the above to: 
    function repeat<T>(n: number, x: T) {
        var l : T[] = [];
        for (var i = 0; i < n; i++)
        {
            l.push(x);
        }
        return l;
    } And this function returns 

`T[]`. This might all seem a bit academic, but I think it's huge. As an example take the library [Underscore.js][6]. This provides a bunch of utility functions for working on lists and collections (familiar to the functional programmer/LINQ user, and some of which are in some JS implementations but not present on all browsers). Prior to 0.9, many types would incorporate `any`, eg `rest` which returns the rest of a list after skipping the first (or first n) element(s): 
    export function rest(array: any[], n?: number): any[];
    
    var z = _.rest([1,2,3,4,5], 2); // z : any[]
    z[0].foo(); // typechecks - z[0] is any Now this can return the same type of array: 

    export function rest<T>(array: List<T>, n?: number): T[];
    
    var z = _.rest([1,2,3,4,5], 2); // z : number[]
    z[0].foo(); // doesn't typecheck - z[0] is number

### Knockout {#knockout} Knockout has the notion of observable properties. To create an observable property, we wrap it in a call to 

`ko.observable`: 
    var viewModel = { 
        z = ko.observable(5);
    }; The pre-generic typing of Knockout had some fixed overloads for built-in types: 

`number` and `string`. However, if you used a user-defined type, you were cast back into `any`-land. Now with generics we can have an `Observable<T>` type. 
    class Foo {
        constructor(public x: number) {}
    }
    var viewModel = { 
        z: ko.observable(new Foo(42))
    }; Note, I did not specify the type of the observable. If I run 

`tsc -d` on the above input I get the resulting types inferred: 
    declare class Foo {
        public x: number;
        constructor(x: number);
    }
    declare var viewModel: {
        z: KnockoutObservable<Foo>;
    }; The difference between these two typings is that the following mistake will be rejected by the generic typing: 

    viewModel.z().y = 42;

## Conclusion {#conclusion} The addition of generics to 

[Typescript][2] probably marks the point where if you're thinking of trying it, it's worth doing so. This is particularly true if your favorite JavaScript libraries have typing definitions available (again, check [DefinitelyTyped][3]). There are a number of other improvements in these recent releases ([0\.9][4] / [0\.9.1][5]) - overloading on constants is also quite funky. It's also worth trying `--noImplicitAny`, though you might well decide it's not for you. *This article was originally published at <http://www.scottlogic.com/blog/2013/09/10/typescript-0.9.html>*[][7]

 [1]: /authors/nicholas-wolverson/
 [2]: http://www.typescriptlang.org/
 [3]: https://github.com/borisyankov/DefinitelyTyped
 [4]: http://blogs.msdn.com/b/typescript/archive/2013/06/18/announcing-typescript-0-9.aspx
 [5]: http://blogs.msdn.com/b/typescript/archive/2013/08/06/announcing-0-9-1.aspx
 [6]: http://underscorejs.org
 [7]: http://www.scottlogic.com/blog/2013/09/10/typescript-0.9.html