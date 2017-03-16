---
ID: 119
post_title: Backbone Model Unit Testing with Jasmine
author: admin
post_date: 2013-04-08 12:20:13
post_excerpt: ""
layout: post
permalink: >
  http://modern-web.dev/2013/04/08/backbone-jasmine/
published: true
dsq_thread_id:
  - "2809872711"
om_counter:
  - "390"
---
*By [Ken Tabor][1]* Writing reliable JavaScript code at scale is difficult. The language lacks built-in formal structures that enable reliable engineering practices. Fortunately establishing conventions and selecting mature libraries can go a long way towards building a trustworthy application architecture. Furthermore, writing unit tests can give us confidence in our applications. In this article I'll discuss unit testing JavaScript web apps, specifically how to use [Jasmine ][2]for testing objects that inherit from Backbone.Model. In particular we'll review simulating service responses and exercising accessor functions. 
## Sample Application I wrote a sample app that supports this article by putting the concepts discussed into practice. If you want to get your hands on something and poke around feel free clone it from here: 

<https://github.com/KDawg/JasmineTestingBackBoneModel> Once you pull down the code, you can run the tests by opening the JasmineTestingBackBoneModel/tests/SpecRunner.html file in Chrome. Jasmine's unit test reporting looks like: [<img class="alignnone size-full wp-image-120" src="http://flippinawesome.org/wp-content/uploads/2013/04/BBModel_JasmineRunner.jpg" alt="BBModel_JasmineRunner" width="397" height="427" />][3] When you have a chance read through SpecRunner.html to get a sense for how it organizes <script> tags to bring in dependent JavaScript libraries, app-specific code, and Jasmine support. It's straight-forward, and if you follow a few basic rules, success will be yours. 
## Jasmine Resources This article isn't intended to train you on Jasmine. If you already know it then cool, but otherwise check out these docs for more domain-specific knowledge. 

*   [Quick intro code reading][2]
*   [Wiki for enhanced details][4]
*   [Stand-alone download][5]
*   [Jasmine GitHub full repo][6]

## What's Up With Mock Data? Pretending to have a service response is far quicker than hitting the real end-point. Jasmine lets us short-circuit a Model.fetch() by immediately returning an object imitating an evaluated JSON response. Is that important? Extremely! Slow tests create friction that programmers will route around. Avoiding running the test suite usually means tests aren't maintained, new tests aren't written, and submitted code is not automatically assured for passing regression. Don't get me wrong, so-called "integration tests" that hit the external services with well-known user accounts and routes are totally worth coding. My warning is because they're naturally slower so, in my opinion, it's worth figuring out how to segregate integration-tests into overnight batch jobs reported by your continuous-integration server. If you need a CI server, I recommend you have a look at 

[Jenkins][7]. Great, now that you're convinced that mocking out the service response makes unit tests respond more quickly, I'll also argue that testing the models doesn't require testing the service or the transmission mechanisms. 
## Organize Tests As If Reading a Story You'll see the Jasmine unit tests are hierarchically organized into larger 

[suites and specs][8] blocked by subject, and those are broken into detailed expectations. Reading them in a cascading flow appears like revealing narrative. For example, in the sample application I provided, they look like this: 
*   A Coffee Model 
    *   has property getting functions that 
        *   should return the name

*   A Coffee Model 
    *   when it fetches 
        *   should be able to parse mocked service response At this point you might be wondering "what's up with the coffee thing?" This example test is written around the defining model for my site "

[Made Fresh Coffee][9]" where people virtually mix tasty ingredients, or pull from a complete recipe list, creating a virtual coffee to send to their favorite people. 
## Code Reading In this section, we'll walk through code snippets from the example project in order to see how Jasmine helps us unit test application code. I'll also explain why I decided to do things a certain way. 

**Reading "Should be able to create its application test objects"** It is my convention for kicking off a unit test by assuring that the simplest things are done. In this test I am creating the thing that needs to be tested and the resources supporting that. 
    it('should be able to create its application test objects',
       function() {
          var coffee = new App.Model.Coffee();
          expect(coffee).toBeDefined();
          expect(MOCK_GET_DATA).toBeDefined();
          expect(MOCK_POST_DATA).toBeDefined();
    }); In this case App.Model.Coffee is the model declared in the application's global namespace. All of that code is brought into the test runner through various <script> tags written in SpecRunner.html. MOCK_GET_DATA is an object defining what a coffee model definition looks like when calling to services. I typically create these mock objects by looking at the "network" table in Chrome's developer tools window while running the app against the live service. I capture that output and use it to write a mock data fixture. For instance, this is the mock data from my sample application: 

    var MOCK_GET_DATA = {
       author: 'Ken Tabor',
       ingredients: [18, 15, 1, 1, 1],
       message: 'Heya everyone, we have a crazy big deadline coming up but I know we can do it. Let's enjoy a coffee and finish strong!',
       name: 'Basic Drip',
       readUrl: 'http://www.madefreshcoffee.com/read.php?sku=bd86292a-241a-11e2-b97c-12313d04a24a'
    };

**Reading "Has Property Getter Functions That…"** A the top of this code block, I create an application object is used to test all the expectations that follow. 
    var coffee = new App.Model.Coffee(MOCK_GET_DATA);

**Reading "…Should Return the Name"** This tests that calling a function on the app object returns the expected value. For example, in the this code, I am ensuring that calling getName() gives me the name value from the mock data above. 
    it('should return the name', function() {
       expect(coffee.getName()).toEqual('Basic Drip');
    });

**Reading "Has Property Setter Functions That…"** This creates its own application model for the suite of expectations that follow. Why bring in a new model? Just in case. Clean-room testing feels like the right thing to do most of the time because it() blocks can easily affect the test objects thereby creating side-effects that can ripple through all your down-stream expectations. Feel free to use your own judgement on this technique. **Reading "…Should Set the New Ingredients"** This block calls the model's set function with an intended value, and then expects to find the proper values stored in the Backbone attribute. Why not use the app object's corresponding .getIngredients() function? A fair question and we could. Perhaps its just a matter of taste, but in this case I prefer the idea of testing more deeply by looking into the implementation details. 
    it('should set the new ingredients', function() {
       coffee.setIngredients([24, 22, 22, 9, 3]);
       expect(coffee.get('ingredients')).toEqual([24, 22, 22, 9, 3]);
    }); Reading "When It Fetches" Now we're getting to a fun test. 

[Jasmine "spies"][10] are used for switching the context from a JavaScript function and replacing it with one of Jasmine's own. Spies observe caller information and can return with its own results (unit test) or just pass through (integration test). My example code shows one way that you can put spies into action. In this case, I am hijacking Backbone.Model.fetch() calling $.ajax() and instead returning my mock data (MOCK_GET_DATA). [Jasmine beforeEach() and afterEach()][11] statements are used in another attempt at being more professional and scientific by creating clean well-known test objects before each it() block. Jasmine will call beforeEach before each it() block and afterEach after each it() block. No surprises there. 
    describe('when it fetches', function() {
       var coffee;
    
       beforeEach(function() {
          spyOn($, 'ajax').andCallFake(function(options) {
             options.success(MOCK_GET_DATA);
          });
          coffee = new App.Model.Coffee();
          coffee.fetch();
       });
    
       afterEach(function() {
          coffee = undefined;
       });
    });

**Reading "should be able to parse mocked service response"** This may seem a bit procedural, but it is designed to confirm an assumption that the proper data is available by checking each Backbone.Model attribute expected from a service response. 
    it('should be able to parse mocked service response', function() {
       expect(_.isEmpty(coffee.attributes)).toEqual(false);
       expect(coffee.get('author')).toEqual('Ken Tabor');
       expect(coffee.get('ingredients')).toEqual([18, 15, 1, 1, 1]);
       expect(coffee.get('message')).toEqual('Heya everyone, we have a crazy big deadline coming up but I know we can do it. Let's enjoy a coffee and finish strong!');
       expect(coffee.get('name')).toEqual('Basic Drip');
       expect(coffee.get('readUrl')).toEqual('http://www.madefreshcoffee.com/read.php?sku=bd86292a-241a-11e2-b97c-12313d04a24a');
    }); Your model might have a more complicated .

[parse][12]() function perhaps that synthesizes new attributes after having unpacked and interpreted the service response. In that sort of scenario, you can see where this would be an even more necessary test. **More Tests Remain for Reading** Of course there are more tests in this sample spec. Please read them when you pull down the example code from my GitHub repo. For example, the one checking $.ajax parameters is unique. 
## A Few of My Favorite Favorite Matchers Yes,I actually have favorite matchers. 

[Jasmine matchers][13] compare a test object result with an expected value. I keep my toolbox lightweight, which assures that what I use, I use often and well. These are my favorites: 
*   .toEqual(‘booga booga');
*   .toBeDefined();
*   .toBeUndefined(); I recommend you consider using and mastering just a few matchers to start. Dip into the Jasmine docs from time to time drawing up a few more features and incorporate them into your project when you're feeling confident. You should also know that other Jasmine matcher libraries exist and you ought to explore them either to use straight-up or to serve as a jumping-off point for building your own. For example, I particularly admire 

[jasmine-jquery][14] because it exposes semantic tests for DOM elements. 
## Further R&D Left to the Reader My article is only the start of proper JavaScript unit testing. You'll want to explore some of the following techniques for professional engineering: 

*   Bring in a [Jasmine "console reporter"][15] for reporting text-only useful for Terminal or in a continuous-integration build process;
*   Using [GruntJS][16] for localhost background watching and to test auto-execute on source file change;
*   Integration testing, which is basically using .andCallThrough() instead of .andCallFake()
*   [RequireJS][17] and it's impact on a testing framework;
*   Seeking out, or rolling your own, [Jasmine custom matchers][18].

## Take Advantage of Jasmine Unit testing using Jasmine is a wonderful way to power up the professional quality of your web app. Dynamic languages like JavaScript are nimble and flexible, but they don't necessarily help craft fantastically stable application architecture. However, unit testing with Jasmine can build up your confidence level thereby making your programming life easier. Have a coffee and watch your application grow in a reliable manner. I welcome questions on twitter: 

[@KenTabor][19] *Source: This article was originally published at <http://blog.katworksgames.com/2013/03/30/bb_model_test_jasmine/>*

 [1]: /authors/ken-tabor/
 [2]: http://pivotal.github.com/jasmine/
 [3]: http://flippinawesome.org/wp-content/uploads/2013/04/BBModel_JasmineRunner.jpg
 [4]: https://github.com/pivotal/jasmine/wiki
 [5]: https://github.com/pivotal/jasmine/downloads
 [6]: https://github.com/pivotal/jasmine
 [7]: http://jenkins-ci.org/
 [8]: https://github.com/pivotal/jasmine/wiki/Suites-and-specs
 [9]: http://www.madefreshcoffee.com/
 [10]: https://github.com/pivotal/jasmine/wiki/Spies
 [11]: https://github.com/pivotal/jasmine/wiki/Before-and-After
 [12]: http://backbonejs.org/#Model-parse
 [13]: https://github.com/pivotal/jasmine/wiki/Matchers
 [14]: https://github.com/velesin/jasmine-jquery
 [15]: https://github.com/larrymyers/jasmine-reporters
 [16]: http://gruntjs.com/
 [17]: http://requirejs.org/
 [18]: https://github.com/pivotal/jasmine/wiki/Matchers#writing-new-matchers
 [19]: https://twitter.com/kentabor