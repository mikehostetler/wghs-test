---
ID: 290
post_title: >
  Quick and Easy Local and Device Web App
  Testing
author: admin
post_date: 2013-05-06 11:55:10
post_excerpt: ""
layout: post
permalink: >
  http://modern-web.dev/2013/05/06/quick-and-easy-local-and-device-web-app-testing/
published: true
dsq_thread_id:
  - "2814945807"
om_counter:
  - "32"
---
*By [Brian Rinaldi][1]* Years ago when I started in web development and for many years thereafter, local testing was a pain. Early on, working in Windows, I would use the local IIS server. This was easy to set up but you could only ever have one site (unless you were running a Windows server edition which few were). There were a number of kludgy workarounds and tools, but the limitation always existed. Later on, when I worked on Mac, I didn't have this issue, but I needed to learn how to modify Apache configuration files, which, while not terribly difficult, could sometimes be a pain. All of this was simply overkill anyway if all I wanted to do was test some front-end design or JavaScript running locally. Nonetheless, nowadays, simply testing locally isn't enough. I need to see my work running on device screens as well. Preferably, I'd like to do so without having to resort to connection sharing or remote hosting. Thankfully, the solutions today make this painless. In this post, I'll look at some quick and simple ways to fire up a local web server to test your designs or JavaScript code in the browser. Then we'll see how you can use that local server along with Adobe Edge Inspect, to easily test on multiple devices. 
## Simple Local Servers For local testing, many times the file:/// protocol works just fine. However, as any designer or developer knows, it has limitations. In those cases, who wants to fuss with any sort of configuration files or setup just to do simple HTML, CSS or JavaScript testing? A better option would be to be able to fire up a local web server right in my working files directory. Thankfully there are a ton of solutions to do just that. The two options I am going to mention today are both built in NodeJS but there are other options out there that don't require you install Node (for example, on Windows I was using 

[Mongoose][2] for a while). [**httpster**][3] httpster is about as simple a local web server as you can get, by design. Assuming you have NodeJS installed on your machine, you can install it using: 
    npm install -g httpster To use it, simple change directory (CD) into your site root and enter the following on the command line: 

    httpster Now simply open the URL http://localhost:3333 in your browser and there you go. Now, you can specify an alternative port if you wish. This is particularly useful as it allows you to have multiple servers running at the same time. 

    httpster -p 8080 You can also specify the subfolder in which to launch the server. Again, this could be useful if you are in a site root and want to quickly launch multiple servers across different folders. Or, alternatively if your project root and your site root are in different directories. 

    httpster my_site/

[**Node-Static**][4] Node static works very similarly to httpster but offers a small handful of additional options if you require them. Obviously, installing is basically the same: 
    npm install -g node-static To launch a local server, simply CD into the site root and enter the command: 

    static Just as in the prior example, specify a subfolder to launch in using: 

    static my_site/ Node-Static uses port 8080 by default, so you'd load http://localhost:8080 in your browser to load the page, or you can specify a port when launching: 

    static -p 3333 So far, all of this is the same. However, Node-Static also lets you specify header information. For example, you can quickly control the max-age header setting (which defaults to 3600 and implies seconds, by default 60 minutes) like so: 

    static -c 600 Additionally, you can specify just about any header you want by supplying them in JSON format. The documentation shows that you can just add the JSON inline via the command-line when starting Node-Static, but that never seemed to work for me (even directly copying their example). However, an easier solution is to simply provide a file that specifies those headers (I like this solution since it's much easier to duplicate). For example, here is a sample headers file I have named headers.json and placed in my project root directory: 

    {
        "Cache-Control": "no-cache, must-revalidate",
        "Referer": "http://remotesynthesis.com"
    } Now I simply load my local sercer specifying that file for the headers: 

    static -f headers.json Opening Chrome Developer Tools, you can see that the referer and cache-control headers I specified are there in the screenshot below. 

[<img class="size-full wp-image-291 alignnone" src="http://flippinawesome.org/wp-content/uploads/2013/05/node-static-headers_sm.png" alt="node static headers" width="600" height="723" />][5] This kind of flexibility can be very useful for more advanced testing scenarios. 
# Device Testing Now that we're done testing locally on our laptop/desktop, we probably want to make sure our site looks good across multiple devices. Perhaps we are testing some responsive design aspects in our site or we need to test certain JavaScript on various device browsers. Well, using our simple local server, we can easily do this using 

[Adobe Edge Inspect][6], and even use developer tools to do debugging. Installing Edge Inspect is a simple three-step process: 
1.  Install the Edge Inspect desktop application (this is available via a free [Creative Cloud][7] membership, though the paid subscription version removes some limitations);
2.  Install the [Chrome Extension][8];
3.  Install the Edge Inspect app on your [iOS][9] and [Android][10] devices. Open Edge Inspect on your device (note: for this to work, it needs to be on the same WiFi network as the laptop/desktop). The first time you connect any particular device to your desktop, you'll need to enter a passcode as in the screenshot below. However, on subsequent uses of this connection this is not required. Now that we've connected, we can browse the local site (running on a local server using httpster or node-static in this example) simply by opening it in Chrome. Edge Inspect will mirror your browsing on any and all connected devices. The free version limits you to a single device connection at a time, but the paid subscription will allow you to connect any number of devices at once and do things like take screenshots of all the connected devices. Also, while Edge Inspect is running, the device will stay on, removing the need to turn on and unlock devices every time you need to view a change. Not only does Edge Inspect allow you to view your page on connected devices, you can also debug them in a similar fashion as you do within Chrome Developer Tools. You can view the HTML, see elements highlight on the device as you select them in the HTML and even make changes to the HTML and CSS to test. Lastly, you can use your trusty console.log() and see the output from each device. Below is a simple screenshot of a console log in my Chrome Developer Tools side by side with the same from the page running in Edge Inspect on my iPad. 

[<img class="alignnone size-full wp-image-292" src="http://flippinawesome.org/wp-content/uploads/2013/05/edge-inspect-debug_sm.png" alt="edge inspect debug" width="650" height="460" />][11] 
## Where to go from here Things have definitely improved from the early days of web development I described earlier. As you can see, it is actually pretty trivial to get a local web server up and running to properly test your web applications. Then, using that simple local server, you can test the same page, running locally, across multiple devices at the same time and even use debugging tools. 

*Image by Jeremy Keith courtesy of <http://commons.wikimedia.org/wiki/File:Cuddling_with_multiple_devices.jpg>*

 [1]: /authors/brian-rinaldi
 [2]: https://github.com/valenok/mongoose
 [3]: http://simbco.github.io/httpster/
 [4]: https://github.com/cloudhead/node-static
 [5]: http://flippinawesome.org/wp-content/uploads/2013/05/node-static-headers_sm.png
 [6]: http://html.adobe.com/edge/inspect/
 [7]: https://creative.adobe.com/
 [8]: https://chrome.google.com/webstore/detail/adobe-edge-inspect/ijoeapleklopieoejahbpdnhkjjgddem?hl=en
 [9]: https://itunes.apple.com/us/app/adobe-edge-inspect/id498621426?mt=8
 [10]: https://play.google.com/store/apps/details?id=com.adobe.shadow.android&hl=en
 [11]: http://flippinawesome.org/wp-content/uploads/2013/05/edge-inspect-debug_sm.png