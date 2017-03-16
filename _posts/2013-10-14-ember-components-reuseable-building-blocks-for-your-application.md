---
ID: 1078
post_title: >
  Ember Components – Reusable Building
  Blocks For Your Application
author: admin
post_date: 2013-10-14 11:36:20
post_excerpt: ""
layout: post
permalink: >
  http://modern-web.dev/2013/10/14/ember-components-reuseable-building-blocks-for-your-application/
published: true
---
*By [Ryan Anklam][1]* Web components promise to bring reusability and encapsulation to the next version of JavaScript. The bad news is that their power is out of reach until mainstream browsers fully implement the ECMAScript 6 standards that make up web components. Fortunately for Ember.js developers, the power of web components can be leveraged today in the form of Ember components. Ember components share more than a similar name with Web Components, they aim to adhere as closely to the W3C Web Component spec as possible. In this article, I'll show you how to use Ember components to build a reusable building block for a scrum card management application. For those of you who have never used Ember before, don't worry, we'll also cover some of the basics of Ember.js that you need to know. 
## Brief Introduction To Ember {#briefintroductiontoember} Lets start with a brief introduction to Ember.js (if you are already familiar with Ember you can skip ahead to the 

*Adding Components To The Story* section). Ember.js is an ambitious web application development framework and covering it in detail is out of the scope of this article, however the following section should give you the knowledge of Ember's building blocks that you'll need to start talking working with components. At a very high level, Ember applications are composed of templates, routes, and controllers. Ember automatically wires all of these components together by sticking to strict naming conventions. For example, a `foo` object would have a template named `foo`, a route named `FooRoute`, and a controller named `FooController`. 
### Handlebars Templates {#handlebarstemplates} Templates in Ember applications are written with the 

[Handlebars][2] templating framework. Variables and helpers in handlebars templates are surrounded by double curly braces: `{{` and `}}`. Helpers are special expressions that allow you to inject logic into a template, for example the `{{ #if }}` and `{{else}}` helpers add conditional logic into templates, or the `{{ #each }}` helper which allows a template to loop through the items of an array. In addition to the helpers that come with Handlebars, Ember extends Handlebars with some Ember specific helpers. A few that you'll see a lot of in an Ember applications are: 
*   `action` helpers send actions to routes and controllers;
*   `link-to` helpers are used to modify an appliations state through an applications routes;
*   `bind-atr` helpers bind attributes to the properties of a controller.

### Routes {#routes} Routes in Ember manage the application's state through the url. At a very high level, a route tells the application which view to display and what model (or data) to populate it with. Routes and views are wired together through Ember's naming conventions. A view with a name of 

`Foo` will have a corresponding route named `FooRoute`, if this route does not exist Ember will create it for you, in memory, behind the scenes. Another important thing to note is that if `FooRoute` does not exist, Ember will create it for you in memory behind the scenes. 
### Controllers {#controllers} Controllers are used to add logic and events to an application's views. Controllers also store values of a view’s properties by either by storing the value as it’s own property or by proxying the value to a model. Ember has three types of controllers: base controllers, array controllers, and object controllers. Each type of controller is distinguished by the way the underlying models are represented. Array controllers are used when a view’s model data is stored in an array. A view with an ArrayController can use the 

`{{each}}` helper to loop through each object in it’s model using the special `{{each controller}}` action.. Array controllers can also use Ember’s Array methods on their models by using the `this` keyword, for example calling `this.filter()` on an ArrayController will run the filter on each of the controller's models. Views that have their data stored in objects are represented by ObjectControllers. ObjectControllers are unique because they proxy their variables directly the model represented by the view. For example, a view that references a variable called `{{name}}` will get its `name` value from the controller which will return the `name` value of it’s model. Controllers are also used to respond to events triggered on a view. In Ember, events are represented by `actions`. Views use the `{{action}}` helper to send events to the controller and the controller uses an `actions` property to store callback functions for events. 
### Models {#models} Models in Ember applications can be plain JavaScript objects or representations of the much more robust 

[ember-data][3] framework. 
## Back to Components {#backtocomponents} Ember components allow developers package together common display elements and functionality in an encapsulated manner that allows them to be reused throughout an application or even in other applications. Because of this encapsulation, data must be explicitly passed into a component and actions must be explicitly passed out of the component into its enclosing controller. This is an important concept to grasp so let me put it another way. As a result, all the data that is used by a view must be manually passed into the view through the components tag. In this article, we'll build a simple scrum backlog management application that utilizes the power of components. To illustrate this, our application will manage two different backlogs (if you are unfamiliar with the concept of a backlog, just think of it as a list of items that need to be done): the active backlog which has current work items and an archive backlog that will store completed items. I’ll call each of these todo items a “card”. 

### Get Boostrapped {#getboostraped} The quickest way to get started building an Ember application is with the 

[Ember Starter Kit][4]. This is a zip file that includes Ember and all of it's dependencies. There is also a very convenient custom JS Bin location that comes with the Ember Starter Kit already configured at <http://emberjs.jsbin.com/>. We'll use the JS Bin version of the starter kit in this article. 
### It all starts with a template. {#itallstartswithatemplate} All Ember components must have a template. Like everything else in Ember, there is a strict naming convention that must be adhered to. The first part of this convention determines that the template's name must start with the text 

`component`, followed by the actual name of the component. The second part of this convention is that the name of the component must include a hyphen, which follows the [W3C spec][5] that also declares that a web component name must include a hyphen. This convention is in place to avoid potential naming conflicts with other HTML tags. Keeping the naming conventions in mind let's start our scrum card component by creating the scrum card template and giving it a name of `componentsscrum-card` by adding the following code to your HTML: 
    <script type="text/x-handlebars" data-template-name="components/scrum-card">
        <article>
            <h1 class="card-title">{{card.title}}</h1>
            <ul>
            {{#each card.tasks}}
              <li>
                <div>
                  <div class="inline" >{{input type="checkbox" checked=completed }}</div>
                  <div {{bind-attr class=":inline completed:isCompleted"}}>
                    <div><strong>{{title}}</strong></div>
                    <div><label>Assigned to: </label>{{assignedTo}}</div>
                  </div>
                </div>
              </li>
            {{else}}
              <li>There are no tasks for this item</li>
            {{/each}}
            </ul>
            {{#unless card.complete}}
            <button {{action 'done' card }}>Done</button>
            {{/unless}}
        </article>
    </script>
    

#### Lets break that down {#letsbreakthatdown} There are a lot of things going on in this template and I'd like to spend some time going over each of them before we continue. One of the first thing's you'll notice is that the Handlebars expressions are referencing properties of a 

`card` object. This object must be explicitly passed into the component upon creation, as the component has no access to the data that is available to it's parent view. A few lines down this template is making use of the `{{#each}}` Handlebars helper. This is an Array helper and will loop through all the items in the array. Note that this helper begins with a `#`, which is the syntax for a Handlebars block helper and which must also have a closing tag. Near the end of the loop there the template also uses `{{else}}` tag. The content inside the `else` tag will onlybe displayed if the array passed into the each block is empty or does not exist. This view also makes use of a special type of Handlebars helper called an Ember view helper: `{{input type="checkbox" checked=completed }}`. Ember view helpers differ from standard HTML input tags because they allow developers to easily bind properties to an existing model. In this case, the model's `completed` property is bound to the checkbox's `checked` property. This piece of code is also particularly interesting because it seemingly breaks the component's tight encapsulation and will update the values of the object that was passed into the component. The next line includes another Ember specific Handlebars helper: `{{bind-attr}}`. This helper binds an element's class name to the property of an object. In this case the `div` will get a class of `isCompleted` when the model's `completed` property is true. The class is removed if the `completed` property is false. Looking back to the explanation of the Ember input view helper you'll see that when the input is checked the value of the completed property will change, which will also change the class of the `div`. That was a mouthful, but perhaps looking at the application [here][6] will give you a clearer picture. 
#### Inserting Components Into Templates {#addingthecomponentintotemplates} Once the component is defined inserting it into a template is as easy as adding an expression to a Handlebars template. For example, in order to add the scrum-card component to a template, simply add the following code in a Handlebars template: 

`{{scrum-card}}`. Since the component does not have access to the same controller data that it's parent template has, the component will not have any of it's expressions replaced by valid data. Data must be passed into a component by adding attributes to the components invocation. If we look back at the scrum-card’s template, we’ll see that it makes heavy use of a variable called `card`. In order to pass the `card` data into the component, add a `card=` attribute inside the `scrum-card` helper and set it's value to a valid card object. (Note: the name of the variable that holds the data does not have to be the same as the variable used in the component. In other words, the left hand side of the attribute must be the name of the variable used in the component’s markup and the right hand side of the attribute can be the name of any valid variable.) The example below loops through an ArrayController's model and passing in the required `card` information into the `scrum-card` component. Notice how the card information passed into the controller is in a variable called `pbi`: 
      <script type="text/x-handlebars" data-template-name="index">
        <h1>Active Backlog</h1>
        {{ #each pbi in model }}
          {{ scrum-card card=pbi }}
        {{ /each }}
      </script>
    

### The Component Object {#thecomponentobject} Perhaps by this point you've asked yourself a few questions: Can I control what element Ember will use to wrap my component's markup when it's inserted into the DOM? How do I get my components to respond to events? Where are the properties that only my controller uses stored? The answer to all of these questions lies in the 

`Ember.Component` object. In order to create an Ember Component object, extend the `Ember.Component` object. As with most things in Ember, naming conventions will determine this object's name, which should be a camel case representation of the component's name. The following code creates the component object for our scrum card. 
    App.ScrumCardComponent = Ember.Component.extend( {
       …
    } );
    

#### Sending and Responding to Actions {#sendingandrespondingtoactions} In order to send actions to the component object the 

`{{action 'actionName' }}` helper is used. Action helpers are nested inside of HTML tags. For example, to send an action called *itWasClicked* to the a component's object when a user clicks on a specific `<div>` the following markup should be added a component's template: `<div {{action 'itWasClicked'}}>`. In Ember controllers and components, actions are always implemented by adding an `actions` property to the object’s declaration (see the [Ember documentation][7]). Each property of the action object should have the same name as the action defined in the action helper and the value of each property is a function that will be executed when the event is triggered. To demonstrate this let's add a button to our scrum card component that will allow us to mark a card as "done". In order to do this we'll add a `<button>` to our template’s markup that will send the "done" action to our component’s object. In order to do this, we need to tell the action handler what card we are marking as done. We'll pass the `card` variable to the action by adding it as an attribute to the action helper. In order to make our UI as simple to use as possible, we'll hide the done button for tasks that have already been completed. The Handlebars `{{#unless}}` helper makes adding this logic to our template easy. This helper tells Handlebars to display the contents between the `{{#unless}}` and `{{/unless}}` tags only if the value of the property passed into the helper is `falsy`. The mirror image of `{{unless}}` is the `{{if}}` and `{{/if}}` helpers which displays information only if the value or the property passed into the helper is `truthy`. Now we can add the done button to our scrum-card by adding the following markup: 
    {{ #unless card.complete }}
      <button {{ action 'done' card }}>Done</button>
    {{ /unless }} We'll also create our object controller and tell it to respond to 

`done` actions by adding the following JavaScript to our application: 
    App.ScrumCardComponent = Ember.Component.extend({
      actions : {
        done : function(){
          this.sendAction( );
        }
      }
    

#### Passing Events To Controllers {#passingeventstocontrollers} There are a number or situations where an application's controllers need respond to events from a component. In order to do this, we need to explicitly tell the template that includes the component what actions the component will send by setting an 

`action` attribute in our template invocation. These events are then sent to the controller through a `sendAction()` method implemented on the component's object, which is then responded to by a method in the controller's action object. [<img src="http://flippinawesome.org/wp-content/uploads/2013/10/ember_controllers.png" alt="ember_controllers" width="700" height="200" class="alignnone size-full wp-image-1117" />][8] Lets start to add this functionality to our scrum-card component by adding an `action` attribute to our invocation markup and giving it a value of `markCardDone` (it will be up to the parent template's controller to respond to the `markdCardDone` event): 
    {{ scrum-card card=card action="markCardDone" }} Before we can tell the controller to respond to the 

`markCardDone` action, it will also need to know which card was clicked on. In order to do this, the `done` method needs to be refactored to add two parameters to `sendAction`: the first one is a string with the text `action` and the second one is the card that was clicked on. To send more than one parameter into the controller simply add them as additional parameters to the `sendAction` method. Controllers can intercept and respond to actions by adding them to their `actions` object. Lets look at how our scrum card application sends "done" events to the controller object and then along to a controller. First we add the action to our component's object: 
    App.ScrumCardComponent = Ember.Component.extend({
      actions : {
        done : function(){
          this.sendAction( 'action', this.get( 'card' ) );
        }
      }
    } );
     Second we will respond to the action in our application's controller, accepting the 

`card` information as an argument: 
    App.IndexController = Ember.ArrayController.extend({
      actions : {
        markCardDone : function( card ){
            this.set( 'model', this.without( card ) );
            archiveItems.push( card );
        }
      }
    });
    

#### Changing the Component's HTML Element {#changingthecomponentshtmlelement} Lets say we want to wrap our component in a more semantic HTML tag like 

`<section>`. To do this simply add a `tagName` property to the component object like so: 
    App.ScrumCardComponent = Ember.Component.extend({
        tagName : 'section',
        …
    } );
    

## There's Much More to Learn {#theresmuchmoretolearn} This tutorial covered many of the basic use cases of Ember components, but it's far from an exhaustive review. For example, you can build components that allow users to pass in their own markup for part of all of the component using block level compoonent tags or bind different class information to our component based on the values of it's properties. To read more on components check out the excellent documentation on the Ember.js website at 

<http://emberjs.com/guides/components/>. I'd also recommend you take a few hours and walk through the [Getting Started][9] guide which will walk you through building a complete Todo application using components. 
### Demo {#demo} A working demo of the scrum card application can be found here: 

<http://emberjs.jsbin.com/abOviQu/1/edit>

 [1]: /authors/ryan-anklam
 [2]: http://www.handlebarsjs.com
 [3]: https://github.com/emberjs/data
 [4]: https://github.com/emberjs/starter-kit/archive/v1.0.0.zip
 [5]: http://www.w3.org/TR/2013/WD-components-intro-20130606/#custom-element-section
 [6]: http://emberjs.jsbin.com/USoHuju/1/edit
 [7]: //emberjs.com/guides/templates/actions/) for more information about implementing actions in templates and controllers
 [8]: http://flippinawesome.org/wp-content/uploads/2013/10/ember_controllers.png
 [9]: http://emberjs.com/guides/getting-started/