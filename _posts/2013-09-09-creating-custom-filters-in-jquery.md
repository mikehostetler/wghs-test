---
ID: 951
post_title: Creating Custom Filters in jQuery
author: admin
post_date: 2013-09-09 11:12:50
post_excerpt: ""
layout: post
permalink: >
  http://modern-web.dev/2013/09/09/creating-custom-filters-in-jquery/
published: true
dsq_thread_id:
  - "2811343621"
om_counter:
  - "42"
---
*By [Aurelio De Rosa][1]* In some cases you may need a shortcut to collect elements for which jQuery doesn't provide a specific filter. This is exactly where custom filters come into play. In this article, based on my book [Instant jQuery Selectors][2], you'll learn how to build a custom filter in [jQuery][3]. Please note that the example in this article includes some minor adjustments compared to the original recipe titled Custom filters. 
## Build the Example The goal of our example is to print the length of the elements that have the placeholder attribute set, taking advantage of a previously created custom filter. In addition, to see all of the available options in action, we'll create a second filter to collect all of the elements having a name with less than a given number of characters. To complete the task, follow the steps described below. 

### Step 1 Create a file and rename it as custom-filters.html. 

### Step 2 Open the file using a simple text editor, like Notepad++ on Windows, Sublime on Mac, or Gedit on Linux, or with your favourite IDE. In this file, we'll create a simple form with some typical fields like “Name”, “Surname”, and so on. All these fields have the specification of the type (type attribute) and the name (name attribute), but few of them also have a placeholder set (placeholder attribute). Just copy the HTML code below and save the changes. 

    <!DOCTYPE html>
    <html>
       <head>
          <meta charset="UTF-8">
          <title>Instant jQuery Selector How-to</title>
          <style>
             input,
             select,
             textarea
             {
                display: block;
             }
          </style>
          <!-- Script will be placed here... -->
       </head>
       <body>
          <form name="registration-form" id="registration-form" action="registration.php" method="post">
             <label>Name:</label>
             <input type="text" name="name" placeholder="Name" />
             <label>Surname:</label>
             <input type="text" name="surname" placeholder="Surname" />
             <label>Email:</label>
             <input type="email" name="email" placeholder="Email" />
             <label>Phone:</label>
             <input type="tel" name="phone-number" placeholder="Phone number" disabled="disabled" />
             <label>Privacy:</label>
             <input name="privacy" type="checkbox" checked="checked" />
             <label>Contact me:</label>
             <input name="contact-me" type="checkbox" />
             <label>Sex:</label>
             <select name="sex">
                <option selected="selected" value="m">Male</option>
                <option value="f">Female</option>
             </select>
             <input type="submit" value="Register" />
          </form>
       </body>
    </html>

### Step 3 Replace the comment we put in the HTML (

`<!-- Script will be placed here... -->`) with the following code: 
    <script src="http://ajax.googleapis.com/ajax/libs/jquery/1.10.1/jquery.min.js"></script>
    <script>
        $.expr[':'].placeholder = function(elem) {
            return $(elem).attr('placeholder') !== undefined;
        };
        $.expr[':'].nameLengthLessThan = 
        $.expr.createPseudo(function(filterParam) {
            var length = parseInt(filterParam);
            return function(elem, context, isXml) {
                return $(elem).attr('name') !== undefined && 
                $(elem).attr('name').length < length;
          }
        });
        $(document).ready(function() {
            console.log($(':placeholder').length);
            console.log($('input:nameLengthLessThan(5)').length);
        });
    </script>

### Step 4 Save the file and open it with your favorite browser. Once the page is loaded, look at the console's output. 

## How it works At the very beginning of our JavaScript instructions for this page, we've added a property, or more specifically a function called placeholder to the 

`:` (yes, it's a property called colon, you read it right) attribute that belongs to the jQuery's `expr` attribute. `:` is a property containing jQuery's native filters and you can use it to add your own at runtime. Inside the function definition, we just have a single statement that checks if the current element has the placeholder attribute set, and if so, it returns true to keep the element. As you can see from the example, in this basic version, a filter is nothing but a function that accepts as an argument the current DOM element to process and needs to return true to keep it in the collection, and false to discard it. You should use this method for creating a filter when the following are true: 
*   You're interested only in the element to process itself
*   The filter doesn't accept an argument
*   The context to which the selection is applied doesn't matter The second custom filter, called 

`nameLengthLessThan`, is slightly more complicated and uses the method introduced (and encouraged) starting from jQuery 1.8. We pass an anonymous function to the `createPseudo` function having a parameter that represents the argument passed to the filter when you use it. Inside it, we create another function that will be returned and that is responsible for performing the filtering. To the latter, jQuery passes the element to be processed (`elem` parameter), the `DOMElement` or `DOMDocument` from which selection will occur (`context` parameter), and a Boolean that tells if you're operating on an XML document. As you may guess, for this filter we need this pattern because our filter needs to know the limit of characters the name attribute of the element must comply with. In other words, we need to pass the number of characters the value of the name attribute must respect. Inside the inner-most function, we write the code to test if the element should be kept or not. For our example, this means checking whether the name attribute is set and its length is not less than the given length (stored using a closure inside the `length` variable). Now that we've created the filters, we need to use them. Inside the handler for the `document.ready` event, there are two statements. The first calls the placeholder filter without parameters and implicitly using the Universal selector. The second uses the `nameLengthLessThan` filter passing 5 as parameter and using the Element selector. Using the Element selector in the second call will result in a performance improvement. Running the code will result in the following being printed in the console: 
    4
    1

## Conclusion In this article I explained how you can create a custom filter in jQuery using the old and the new methods, introduced in jQuery 1.8, methods. It's important to know both because while in new projects you'll hopefully find the latter method, in some projects you may find the former and it can be important that you know how it works, especially if the jQuery version can't be updated. If you liked this article, consider buying my book 

<a href="http://www.packtpub.com/learn-how-to-use-jquery-selectors-effectively/book" target="_blank">Instant jQuery Selectors</a> on the <a href="http://www.packtpub.com/learn-how-to-use-jquery-selectors-effectively/book" target="_blank">Packt Publishing website</a> or on <a href="http://www.amazon.com/Instant-jQuery-Selectors-ebook/dp/B00ESX17MU/" target="_blank">Amazon</a>. Instant jQuery Selectors is for web developers who want to delve into jQuery from its very starting point: selectors. Even if you're already familiar with the framework and its selectors, you could find several tips and tricks that you aren't aware of, especially about performance and how jQuery acts behind the scenes. *This article was originally published at <http://aurelio.audero.it/blog/2013/09/05/how-to-create-custom-filters-in-jquery/>*

 [1]: /authors/aurelio-de-rosa/
 [2]: http://www.packtpub.com/learn-how-to-use-jquery-selectors-effectively/book
 [3]: http://jquery.com/