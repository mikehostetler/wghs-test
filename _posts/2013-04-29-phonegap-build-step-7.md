---
ID: 259
post_title: >
  Create Your First Mobile App with
  PhoneGap Build – Using the
  Notification API
author: admin
post_date: 2013-04-29 11:59:45
post_excerpt: ""
layout: post
permalink: >
  http://modern-web.dev/2013/04/29/phonegap-build-step-7/
published: true
dsq_thread_id:
  - "2809873557"
om_counter:
  - "435"
---
*By [Brian Rinaldi][1]* **This is the 7th and final part in the series. You can find [part 1 here][2], [part 2 here][3], [part 3 here][4], [part 4 here][5], [part 5 here][6] and [part 6 here][7]. The files for the sample app are [available here][8].** In this final step, we will add a finishing touch to our application and use another PhoneGap API for [notifications][9] to create a native popup. We'll begin by creating a new page to view our saved projects. The page will look and behave similarly to the home page list but will be populated with just results from our database query. 
## Building the Page First, let's create a simple page to list the saved items which, again, will be very similar to the home page list. Name this page faves.html. 

    <!DOCTYPE HTML>
    <html>
    <head>
    </head>
    
    <body>
        <div id="favesHome" data-role="page" data-add-back-btn="true">
            <div data-role="header" data-position="fixed">
                <h1>Saved Items</h1>
            </div>
            <div data-role="content">
                <ul id="savedItems" data-role="listview"  data-filter="true">
                </ul>
            </div>
        </div>
    </body>
    </html> Let's add a navigation button to the home page header to navigate to our new page. Note that jQuery Mobile turns any link within the section with a data-role of header into a button. 

    <div data-role="header" data-position="fixed">
    <h1>GitHub Search</h1>
    <a href="faves.html" class="ui-btn-right" data-icon="arrow-r">Saved Items</a>
    </div>

## Loading Saved Items Our saved items page doesn't show anything yet because we need to load the saved items into the list when the page, favesHome, loads via the "pageshow" event. First, we need to load the data from our local database. This code will look similar to the database transaction code from the prior steps. Add this to index.js. 

    $('#favesHome').live('pageshow', function(event) {
        db.transaction(loadFavesDb, txError, txSuccess);
    });
    
    function loadFavesDb(tx) {
        tx.executeSql("SELECT * FROM repos",[],txSuccessLoadFaves);
    } When the results are returned, we'll loop through them to assemble and display the list in a manner very similar to the to the code that builds the list for the home page. You will note that we are simply reusing the project detail page here rather than creating an additional page. Also add this code to index.js. 

    function txSuccessLoadFaves(tx,results) {
        console.log("Read success");
    
        if (results.rows.length) {
            var len = results.rows.length;
            var repo;
            for (var i=0; i < len; i = i + 1) {
                repo = results.rows.item(i);
                console.log(repo);
                $("#savedItems").append("<li><a href='repo-detail.html?owner=" + repo.user + "&name=" + repo.name + "'>"
                + "<h4>" + repo.name + "</h4>"
                + "<p>" + repo.user + "</p></a></li>");
            };
            $('#savedItems').listview('refresh');
        }
    }

## Using the Notification API You can test this in your browser at this point and, assuming you have saved a favorite or two, you should see the list populate properly and even be able to view the details page of a saved item. Of course, if you haven't saved any favorites yet, the page simply remains blank. Let's add a notification that let's the user know that nothing has been saved. In order to do that we will use the 

[PhoneGap notification API][9] to launch a native popup. Let's change our txSuccessLoadFaves() method from earlier and add in an else to our if statement block. 
    else {
       if (navigator.notification)
                navigator.notification.alert("You haven't saved any favorites yet.", alertDismissed);
    
    } We added an if statement around our notification API call simply so that it does not error when tested in the browser. However, since this API uses native alerts, it will not show in the browser and you will need to run it on a device to see it function properly. The notification API allows you to define a callback handler for when the alert is dismissed. Let's create this callback handler, and in it we'll use the jQuery Mobile method to navigate the user back to the home page since we have no favorites to display for them. 

    function alertDismissed() {
        $.mobile.changePage("index.html");
    } Below you can see a screenshot of the notification as it appears on my Android device. Clicking "OK" takes you back to the home screen. You can zip up your files and upload them to PhoneGap Build to test them on your device. 

[<img class="alignnone size-full wp-image-263" src="http://flippinawesome.org/wp-content/uploads/2013/04/step5.png" alt="step5" width="330" height="587" />][10] 
## On Your Own Obviously, while our app works, it isn't quite complete. First, it could use some code cleanup. For example, we probably shouldn't be putting all of our JavaScript into one file. Though this was done for the sake of simplicity of the tutorial, for long-term maintenance, we probably should separate out the various portions of our JavaScript code. We should also clean up all the console logs that are there for testing purposes. There are also probably some places we can prevent duplication such as in our database transaction methods and in our two jQuery methods to assemble the list of projects. a Of course there are also a number of user-interface (UI) and functionality improvements we could do. If you want to continue this tutorial on your own, here are some ideas you can use. 

*   Make the favorites persist when the app is closed and reopened (rather than rebuilding the table each time the application loads).
*   Allow the user to delete favorites.
*   Allow the search terms to be specified rather than hardcoded.
*   Allow the user to send repository details to a friend using the Contacts API.

## Additional Resources Hopefully this walkthrough has helped you get started using PhoneGap Build to create mobile apps but clearly this was just the tip of the iceberg. Thankfully there are a lot of great resources out there for you to continue learning. Below are just a few as well as a handful of alternatives to jQuery Mobile for your choice of mobile framework that you might consider checking out. 

**Sample Apps** Andrew Trice - Walkable <https://github.com/triceam/Walkable-App> Andrew Trice - Instant Halloween <https://github.com/triceam/Instant-Halloween> Andrew Trice - US Census Browser <https://github.com/triceam/US-Census-Browser> Andrew Trice - Fresh Food Finder <https://github.com/triceam/Fresh-Food-Finder> Andrew Trice - Li'l Doodle <https://github.com/triceam/Lil-Doodle> Christophe Coenraets - Employee Directory (jQuery Mobile) <http://coenraets.org/blog/2011/10/sample-application-with-jquery-mobile-and-phonegap/> <http://coenraets.org/blog/2011/11/jquery-mobile-getting-started-application/> Christophe Coenraets - PhoneGap API Explorer <https://github.com/ccoenraets/phonegap-explorer> **Videos** Piotr Walczyszyn <http://www.youtube.com/watch?v=YD1kP0a3oBY> AdobeTV (Greg Rewis) - Dreamweaver Panel <http://www.youtube.com/watch?v=ABS_jjNDX7Y&feature=related> AdobeTV (Kevin Hoyt) - PhoneGap (not Build) <http://tv.adobe.com/show/adobe-evangelists-kevin-hoyt/> Video2Brain - Dreamweaver Panel (CS6) <http://www.youtube.com/watch?v=ngLTIV9izds&feature=related> Lynda - Dreamweaver Panel (CS5.5) <http://www.lynda.com/Dreamweaver-CS5-5-tutorials/Building-Android-and-iOS-Apps-with/83788-2.html> Lynda - jQuery Mobile <http://www.lynda.com/jQuery-tutorials/jQuery-Mobile-Web-Applications/104967-2/files> **Mobile Frameworks** Kendo Mobile <http://www.kendoui.com/mobile.aspx> Sencha Touch <http://www.sencha.com/products/touch/> AppUI (Andrew Trice) <https://github.com/triceam/app-UI> Moobile <http://moobilejs.com/> Dojo Mobile <http://dojotoolkit.org/features/mobile> Enyo <http://enyojs.com/>

 [1]: /authors/brian-rinaldi/
 [2]: http://flippinawesome.org/2013/03/29/phonegap-build-part1/
 [3]: http://flippinawesome.org/2013/03/29/phonegap-build-part2/
 [4]: http://flippinawesome.org/2013/03/29/create-your-first-mobile-app-part3/
 [5]: http://flippinawesome.org/2013/04/08/phonegap-build-part4/
 [6]: http://flippinawesome.org/2013/04/15/phonegap-build-step-5/
 [7]: http://flippinawesome.org/2013/04/22/phonegap-build-step-6/
 [8]: http://bit.ly/11PsyPe
 [9]: http://docs.phonegap.com/en/1.0.0/phonegap_notification_notification.md.html#Notification
 [10]: http://flippinawesome.org/wp-content/uploads/2013/04/step5.png