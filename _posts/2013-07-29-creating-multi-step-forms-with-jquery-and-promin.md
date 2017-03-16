---
ID: 741
post_title: >
  Creating Multi-step Forms with jQuery
  and Promin
author: admin
post_date: 2013-07-29 12:22:14
post_excerpt: ""
layout: post
permalink: >
  http://modern-web.dev/2013/07/29/creating-multi-step-forms-with-jquery-and-promin/
published: true
---
*By [Tim Severien][1]* User-interface design is a rapidly developing aspect of design. The challenge is to clarify information or interaction for users while making it as attractive as possible. One of these interactions is something you find on any site: forms. Most forms are just large, bulky collections of fields - very important but often badly implemented. We want to make it functional and look attractive so that the user wants to stay and interact. One solution is obvious: pagination. Any media, digital, analog, online or offline is full of it. You flip pages on magazines; you browse from page to page on a website; it's everywhere. By limiting the visible fields and breaking the form into pieces, the form appears smaller and more manageable. In addition, you utilize your space much more efficiently and, last but not least, it looks better. To make the task of paginating forms easy for developers, I've wrapped it up in a jQuery plugin called [Promin][2]. Promin allows you to serve your form in bits. It supports a range of options to make it fit for your needs. In this article, I'll show you how to get started using the plugin. 
## Getting started First, we need to 

[download the JavaScript file from Github][3]. Optionally, you can [download the CSS file][4] used in the demo too. Once embedded, we need a HTML form just like any other, with only minor modifications to make Promin recognize the steps. 
    <form action="#" id="form" enctype="application/x-www-form-urlencoded" method="post">
        <input type="text" name="name" class="pm-step" placeholder="What is your name?" />
    
        <div class="pm-step">
            <input type="text" name="email" placeholder="E-mail address" />
            <p>E-mail addresses are only used for identification. We do not share or sell them.</p>
        </div>
    </form> Once the form is set up, we have to initialize it with Promin. 

    $('#form').promin(); By default, a set of intuitive shortcuts are available to navigate through the form elements. However, adding buttons to navigate can be really helpful, especially for your less “web savvy” visitors. All elements without the pm-step class are not indexed and thus left intact. 

    <form action="#" id="form" enctype="application/x-www-form-urlencoded" method="post">
        ...
    
        <button id="btn-next">next</button>
    </form> Of course, we need to specify what the button should do. Here's the entire JavaScript that initializes the form and adds the next functionality to the button: 

    $('#form').promin();
    
    $('#btn-next').click(function() {
        $('#form').next();
    }); Lets say I want something to occur on each step in our form process. For example, imagine I wanted to pick a random color from a palette on each step and then set it as background color. This is where events come in handy. We can specify events by sending an input object in the constructor. 

    var colors = ['#f00', '#0f0', '#00f'];
    
    $('#form').promin({
        events: {
            change: function() {
                var index = Math.floor(Math.random() * colors.length);
                var color = colors[index];
                $(document.body).css('background-color', color);
            }
        }
    }); There are a number of additionally supported events including next, previous, submit and reset. 

## Where to Go From Here There's quite a bit more to the Promin plugin. For example, there are a variety of options, shortcuts and methods that are available to help you customize the functionality of your multi-step form. For all the details, please feel free to 

[check out the documentation][2].

 [1]: /authors/tim-severien
 [2]: http://timseverien.nl/promin/
 [3]: https://github.com/timseverien/Promin/blob/master/js/jquery.promin.js
 [4]: https://github.com/timseverien/Promin/blob/master/css/promin.css