---
ID: 203
post_title: >
  Building a Google Maps Application with
  Updating Markers
author: admin
post_date: 2013-04-22 12:13:28
post_excerpt: ""
layout: post
permalink: >
  http://modern-web.dev/2013/04/22/google-maps-markers/
published: true
dsq_thread_id:
  - "2809872944"
om_counter:
  - "683"
featured_author:
  - 'Yes'
---
*by [Raymond Camden][1]* I've been a fan of the Google Map API for some time now. If you dig beyond the basic "Show a map of X" and really examine the API, it is incredibly powerful. Recently, someone asked me an interesting question about Google Maps. Is it possible to use Google Maps to track moving items? I had worked with Google Maps and markers before, but I had never built a demo that included "live" markers that could move. In this article I'll share the proof of concept I built that demonstrates this feature. To download the sample app code, [click here][2]. 
## The Data My demo consists of two parts - the client and the server. The server is not critical to the point of this article so I'll discuss it last, and only briefly. For now, simply assume that we've got a server-side application that can report on the locations of a set of trucks. Each truck has an ID, a name, and position value consisting of latitude and longitude. Here is an example of the dataset: 

    [{"id":1,"name":"Truck 1","position":{"lat":30.23554018035925,"long":-92.01935091053534}},
    {"id":2,"name":"Truck 2","position":{"lat":30.164984466019252,"long":-91.97831393031169}},
    {"id":3,"name":"Truck 3","position":{"lat":30.21268333491144,"long":-91.99062979629551}},
    {"id":4,"name":"Truck 4","position":{"lat":30.26265532616729,"long":-91.98054925137356}},
    {"id":5,"name":"Truck 5","position":{"lat":30.248690018924144,"long":-92.0012589647479}}]

## Building the Sample App In my initial front end code, I simply built the code that would load the map, call the server, and display the markers as is. I'm assuming that you are familiar already with Google Maps (if not, see some related links below), and if so, you already know about the required JavaScript libraries. The only HTML-related aspect of this demo is the div used to store the map. I've given the div an ID of "map_canvas." The JavaScript is where things get interesting. I begin my code with a simple jQuery initialization routine: 

<pre><code class="language-javascript">$(document).ready(function() {

});</code></pre> Due to the simple nature of this demo, all of my JavaScript lives inside of there. I begin by laying out a map centered in Lafayette, LA. That's where I live so it makes sense to begin there. Assume we are a local shipping company and our server-side data represents our trucks. 

<pre><code class="language-javascript">var myLatlng = new google.maps.LatLng(30.2353412,-92.010498);
    var myOptions = {
        zoom: 13,
        center: myLatlng,
        mapTypeId: google.maps.MapTypeId.ROADMAP,
    }
    var map = new google.maps.Map(document.getElementById("map_canvas"), myOptions);</code></pre> By the way, in case you're curious where I got that latitude and longitude value, I used this application: 

[iTouchMap.com][3]. They have a simple little tool that lets you click on a map and retrieve latitude and longitude points. I used it both to center my map as well as create the initial points for my trucks. (I basically clicked in a circle around the center of town.) Next, I created a function that would handle making my Ajax request and displaying the markers. 
<pre><code class="language-javascript">//Time between marker refreshes
var INTERVAL = 2000;

function getMarkers() {
    $.get('/markers', {}, function(res,resp) {
        for(var i=0, len=res.length; i&lt;len; i++) {

        var marker = new google.maps.Marker({
                position: new google.maps.LatLng(res[i].position.lat,res[i].position.long),
                title:res[i].name,
                map:map
            }); 
        }
        window.setTimeout(getMarkers,INTERVAL);
    }, "json");
}</code></pre> Again, the call to "/markers" represents a server-side function I'll discuss later. The important thing to remember is that it returns an array of data. I can take that array, iterate over it, and then simply create a marker for the truck. Note the use of setTimeout at the end. Isn't setInterval better? It can be. But since I'm dealing with an Ajax call, I can't be sure that a particular Ajax request has ended before the next interval runs. So - this by itself handles the basic operation - hit the server, render the markers. 

## Improving the Sample App Whenever I build something I always start off simple and then iteratively update with new functionality. My server-side code already handles moving markers, so now let's look at the updated getMarkers code to handle this. 

<pre><code class="language-javascript">//Used to remember markers
var markerStore = {};

function getMarkers() {
    $.get('/markers', {}, function(res,resp) {
        for(var i=0, len=res.length; i&lt;len; i++) {

            //Do we have this marker already?
            if(markerStore.hasOwnProperty(res[i].id)) {
                markerStore[res[i].id].setPosition(new google.maps.LatLng(res[i].position.lat,res[i].position.long));
            } else {
                var marker = new google.maps.Marker({
                    position: new google.maps.LatLng(res[i].position.lat,res[i].position.long),
                    title:res[i].name,
                    map:map
                }); 
                markerStore[res[i].id] = marker;
            }
        }
        window.setTimeout(getMarkers,INTERVAL);
    }, "json");
}</code></pre> First, make note of the outside variable, markerStore. I want to remember if I've used a marker before and this generic object will help me with that task. If you look back up to the data sample I provided above, you will note that each truck has an ID value. I've updated my loop to look for this ID value and if it exists, use the Google Marker object setPosition call to just move it. Otherwise I create a new marker and store a copy in the marker store. Here's a screenshot of this in action (well, a still shot of course). 

[<img class="alignnone size-full wp-image-204" src="http://flippinawesome.org/wp-content/uploads/2013/04/map-trucks-screenshot.png" alt="map-trucks-screenshot" width="520" height="513" />][4] And that's pretty much it. You could get a bit more fancy and modify the markers so they included an infowindow or perhaps their ID to help differentiate them. 
## Where to go from here For folks curious about the server-side code, I've included it in the zip attached to this article. It is a simple Node application. The only real interesting aspect was figuring out how much I should move the longitude/latitude positions of my trucks. I ended up using a random value between -0.002 and and 0.002. The only real logic behind that was that it "looked" right. Unfortunately, if you run the demo long enough the trucks could easily move off the visible map. Real trucks would (hopefully) stick around town! 

*Related Links:* 
*   [Google Maps API][5]
*   [Simple Google Maps with Custom Markers][6]
*   [Quick Demo of jQuery and Google Maps][7]

 [1]: /authors/raymond-camden
 [2]: http://flippinawesome.org/wp-content/uploads/2013/04/mapmovedemo.zip
 [3]: http://itouchmap.com/latlong.html
 [4]: http://flippinawesome.org/wp-content/uploads/2013/04/map-trucks-screenshot.png
 [5]: https://developers.google.com/maps/
 [6]: http://www.raymondcamden.com/index.cfm/2012/12/3/Simple-Google-Maps-demo-with-Custom-Markers--Followup
 [7]: http://www.raymondcamden.com/index.cfm/2011/8/6/Quick-demo-of-jQuery-and-Google-Maps