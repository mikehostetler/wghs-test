---
ID: 181
post_title: >
  How to create an advanced HTML5
  placeholder polyfill
author: admin
post_date: 2013-04-15 11:55:45
post_excerpt: ""
layout: post
permalink: >
  http://modern-web.dev/2013/04/15/advanced-placeholder-polyfill/
published: true
dsq_thread_id:
  - "2809873393"
om_counter:
  - "125"
---
*By [Aurelio De Rosa][1]* Some new HTML5 features are bigger than others but even some of the small one can be incredibly important and useful. One of these shiny features is an attributes called placeholder which allows you to specify placeholder text inside form's elements like <input> and <textarea>. Unfortunately, older browsers don't support the placeholder feature and some of the existing browser support has limitations such as on styling the placeholder. To support these older browsers, we'll need to use or create a polyfill. If you haven't heard the term polyfill before, [read this article][2] by Remy Sharp, who originally coined the term. In this article I'll walk through how I created a polyfill I named [Audero Unified Placeholders][3], which is a very lightweight cross-browser jQuery plugin designed to emulate the HTML5 placeholder attribute on browsers that don't support it. This placeholder polyfill emulates perfectly the native behavior, for example hiding the placeholder text on the first input of the user rather than on focus. In addition, it allows you to style the placeholder text using CSS and to override the browser's native support for styling (in those that support it). Please note that to promote the "best practices", the plugin will use the [jQuery plugin suggested guidelines][4] as a starting point. 
### The Starting Point The starting point of the JavaScript file that will make up the core of our polyfill is taken from the jQuery guidelines page. The scope of this article doesn't go into detail about creating jQuery plugins (the 

[guidelines][4] do a good job of that) but I will mention a few things about it. To create the plugin, I'll use an IIFE so that the plugin won't collide with other libraries that use the dollar sign, such as Prototype for example. Moreover, I'll also use namespacing to ensure it'll have a very low chances of being overwritten by other code living on the same page. In a very imaginative moment of inspiration, I decided to use auderoUnifiedPlaceholders as namespace. Reading the line 2 of the code below, you can see how I added the namespace as a property of the $.fn object and, instead of adding every method to the latter, I'll put them in an object literal as suggested by the guidelines. Finally, the plugin's methods can be called by passing method's name as a string. 
    (function($){
       $.fn.auderoUnifiedPlaceholders = function (method) {
          if (methods[method])
             return methods[method].apply(this, Array.prototype.slice.call(arguments, 1));
          else if (typeof method === "object" || !method)
             return methods.init.apply(this, arguments);
          else
             $.error("Method " + method + " does not exist on jQuery.auderoUnifiedPlaceholders");
       };
    })(jQuery); Now that we've created the base of our plugin, we need to create the necessary methods. Of course, an essential method would be the init() method to initialize the plugin. This function will specify default values that will be used if a specific value hasn't been passed by the developer. Taking inspiration from the jQuery UI widgets, we'll also add other methods which I'll cover in the subsequent sections: enable(), disable(), reset(), and destroy(). Let's take a look at the default values I discussed. As I said in the introduction, this plugin will work not only as a polyfill but also allows you to create style customizations. So, firstly we'll create a property, called overrideNative, to specify whether the plugin will override the browser's native placeholder support. By default, this will be set to false. We'll allow the developer to set a CSS class that will apply to the selected element using a property called className, and also an object called style that allows them to set CSS properties (like color, font-weight, and so on). By default, the latter will contain only a color property set to the same gray color used by the browsers. Here's the code so far: 

    (function($) {
       var defaultValues = {
          overrideNative: false,    // boolean. If override browsers native placeholder support.
          className: "",            // string. A class to apply to the selected elements.
          style: {color: "#A9A9A9"} // object. A object containing a set of rules to apply to the selected elements.
                                    // The rules in this object will have higher priority among those used
                                    // in the class specified using the className property.
       };
       var methods = {
          init: function(options) {
             /* Code goes here */
          },
          enable: function() {
             /* Code goes here */
          },
          disable: function() {
             /* Code goes here */
          },
          reset: function() {
             /* Code goes here */
          },
          destroy: function() {
             /* Code goes here */
          }
       };
       $.fn.auderoUnifiedPlaceholders = function (method) {
          if (methods[method])
             return methods[method].apply(this, Array.prototype.slice.call(arguments, 1));
          else if (typeof method === "object" || !method)
             return methods.init.apply(this, arguments);
          else
             $.error("Method " + method + " does not exist on jQuery.auderoUnifiedPlaceholders");
       };
    })(jQuery);

### The init() Method Let's take a look at what the init() method does. In order to emulate the placeholder attribute, the plugin uses the form element's value attribute. Firstly, if the user provide an options object, it'll be merged with the default settings using the jQuery extend() method. Then, we'll test if the browser supports the placeholder attribute and whether the user chooses to override the native support. If the browser supports placeholder and the user chooses not to override, the execution will be stopped immediately. Otherwise, the plugin will select all the elements that have a placeholder attribute set (using the filter() method) and loop over them using the each() method. To store the data of the plugin we'll use two data-* attributes, one to store the placeholder's text (data-audero-unp-text) and the second to ensure there aren't any collisions between the text typed by the user, that is the value of the element, and the placeholder's text (data-audero-unp-typed). The first attribute is used to save the placeholder as well as delete the placeholder attribute of the element. The second one is a boolean that indicate whether the current value matches the placeholder, if it has been typed by the user or not. The normal behavior of a placeholder is to disappear as soon as the user types the first character and, since the placeholder is a real text value, the user expects the caret to be placed at the beginning of the placeholder's text itself. Therefore, we need to listen for the following four events: 

*   **focus**: If the field has the placeholder set and the user hasn't typed anything, we'll move the caret to the beginning of the placeholder's text.
*   **keydown**: Remove the placeholder and the style applied to the form element's text. Also, set the data-audero-unp-typed attribute to true.
*   **keyup**: If the value is empty, restore the placeholder and the style (using the blur event callback) and move the caret to the beginning (using the focus event callback).
*   **blur**: If the value is empty restore the placeholder, the placeholder style and set the data-audero-unp-typed attribute to false. Here's the code to accomplish this: 

    $element
    .on("focus.auderoUnifiedPlaceholders", function () {
       if ($(this).val() === placeholder) {
          if (this.createTextRange) {
             var part = this.createTextRange();
             part.move("character", 0);
             part.select();
          } else if (this.setSelectionRange)
             this.setSelectionRange(0, 0);
       }
    })
    .on("keydown.auderoUnifiedPlaceholders", function() {
       $(this).removeClass(options.className);
       for(var rule in options.style) {
          $(this).css(rule, "");
       }
       if ($(this).val() === placeholder && $(this).attr("data-audero-unp-typed") === "false")
          $(this).val("");
       else
          $(this).attr("data-audero-unp-typed", "true");
    })
    .on("keyup.auderoUnifiedPlaceholders", function() {
       if ($(this).val() === "") {
          $(this)
          .trigger("blur.auderoUnifiedPlaceholders")
          .trigger("focus.auderoUnifiedPlaceholders");
       }
    })
    .on("blur.auderoUnifiedPlaceholders", function () {
       if ($(this).val() === "") {
          $(this)
          .attr("data-audero-unp-typed", "false")
          .val(placeholder)
          .addClass(options.className)
          .css(options.style);
       }
    });

### The enable() and disable() Methods As stated in the previous section, this polyfill uses the value property of the elements to replicate the placeholder. Chances are that your form is using some type of plugin to validate the form data inserted by the user. This means that the validator plugin will attempt to validate the values written as placeholders. Another problematic case occurs when the user doesn't fill in a field. In this circumstance, you'll want the server to receive the field's value as empty instead of the placeholder value. To resolve these cases, the plugin includes the ability to be disabled before validating the data or sending it to the server. To achieve this, use the plugin's disable() method. To disable, simply call auderoUnifiedPlaceholders() passing the string value of "disable." Once the data is verified, you can re-enable the plugin using the enable() method simply passing the string "enable" to auderoUnifiedPlaceholders(). The code in these two methods is quite simple. The enable() method checks if the element's value is empty and, if so, set it to the placeholder's text. On the contrary, the disable() method checks if the value is the same of the placeholder and that it hasn't been typed by the user, in which case the value is set to empty. The code of these methods is shown below: 

    enable: function() {
       return this.each(function() {
                 var placeholder = "";
                 if (typeof $(this).attr("placeholder") !== "undefined")
                    placeholder = $(this).attr("placeholder");
                 else if (typeof $(this).attr("data-audero-unp-text") !== "undefined")
                    placeholder = $(this).attr("data-audero-unp-text");
                 else
                    return;
    
                 if ($(this).val() === "")
                    $(this).val(placeholder);
             });
    },
    disable: function() {
       return this.each(function() {
                 var placeholder = "";
                 if (typeof $(this).attr("placeholder") !== "undefined")
                    placeholder = $(this).attr("placeholder");
                 else if (typeof $(this).attr("data-audero-unp-text") !== "undefined")
                    placeholder = $(this).attr("data-audero-unp-text");
                 else
                    return;
    
                 if ($(this).val() === placeholder && $(this).attr("data-audero-unp-typed") === "false")
                    $(this).val("");
             });
    }

### The reset() Method The reset method is useful to solve an issue that your user will face if you put an <input type="reset"/> button in your form. Once the user clicks it, all the elements will be cleared but so will the placeholders because, as you know, they use the value attribute. To offer the expected behavior to your users, you can use the reset() method offered by the plugin. It'll clear the values typed by the user while preserving the placeholders. The reset() method take advantage of the callback attached to blur event shown before. The code for this function is shown below: 

    reset: function() {
       return this
              .filter(function() {
                // Remove elements that haven't neither a placeholder nor the audero unified placeholder
                return (
                        typeof $(this).attr("placeholder") !== "undefined"
                        || typeof $(this).attr("data-audero-unp-text") !== "undefined"
                       );
             })
             .val("")
             .trigger("blur.auderoUnifiedPlaceholders");
    }

### The destroy() Method In some cases (although I can't really imagine them), you may need to destroy the changes applied by this polyfill. In this scenario, the destroy() method will remove all the listeners, the style applied taking advantage of the keydown callback and restore the native placeholder behavior. The body of the method is as follows: 

    destroy: function() {
       return this
              .trigger("keydown.auderoUnifiedPlaceholders")
              .off("focus.auderoUnifiedPlaceholders keydown.auderoUnifiedPlaceholders blur.auderoUnifiedPlaceholders")
              .each(function(index, element) {
                 if (
                      typeof $(element).attr("data-audero-unp-text") !== "undefined"
                      && typeof $(element).attr("placeholder") === "undefined"
                    ) {
                          $(element).attr("placeholder", $(element).attr("data-audero-unp-text"));
                    }
                    $(element)
                        .removeAttr("data-audero-unp-text")
                        .removeAttr("data-audero-unp-typed");
              });
    }

### Using the Plugin Using this plugin is very simple. Just call the auderoUnifiedPlaceholders() method on the element(s) you want to apply the placeholder. For example, let imagine that you have the following code: 

    <form name="registration-form" id="registration-form" action="#" method="get">
        <input type="text" name="name" placeholder="Name" />
        <input type="text" name="surname" placeholder="Surname" />
        <input type="email" name="email" placeholder="Email" />
        <input type="tel" name="phone-number" placeholder="Phone number" />
        <input type="submit" value="Register" />
    </form> A basic call to the plugin would look like: 

    <script>
        $(document).ready(function () {
            $('#registration-form input[placeholder]').auderoUnifiedPlaceholders();
        });
    </script> Here's a more advanced example that uses two of the three possible options: 

    <script>
        $(document).ready(function () {
            $("#registration-form input[placeholder]").auderoUnifiedPlaceholders({
                overrideNative: true,
                style: {
                    "color": "#000000",
                    "font-weight": "bold"
                }
            });
        });
    </script> The above code uses the overrideNative option tol force browsers that have support for the HTML5 placeholder attribute to have the default behavior overridden. Using the style option, the placeholder will be display as black (#000000) and bold. For more examples, take a look at the official documentation included in the repository. 

### Conclusion This article has shown you how to create an advanced polyfill for the HTML5 placeholder attribute. Hopefully this can help you if you choose to create your own polyfill for new HTML5 features. If you'd like to see how this polyfill works in more detail, 

[download the source code][3] and take a look at the documentation included in the repository. [Audero Unified Placeholders][3] is completely free and you also free to change or improve it as it is dual licensed under [MIT][5] and [GPL-3.0][6]. *Background image courtesy of imagecity.com*

 [1]: /authors/aurelio-de-rosa/
 [2]: http://remysharp.com/2010/10/08/what-is-a-polyfill/
 [3]: https://github.com/AurelioDeRosa/Audero-Unified-Placeholders
 [4]: http://docs.jquery.com/Plugins/Authoring
 [5]: http://www.opensource.org/licenses/MIT
 [6]: http://opensource.org/licenses/GPL-3.0