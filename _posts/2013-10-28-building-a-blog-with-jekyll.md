---
ID: 1175
post_title: Building a Blog with Jekyll
author: admin
post_date: 2013-10-28 11:25:48
post_excerpt: ""
layout: post
permalink: >
  http://modern-web.dev/2013/10/28/building-a-blog-with-jekyll/
published: true
dsq_thread_id:
  - "2809851597"
om_counter:
  - "192"
---
*By [Donovan Hutchinson][1]* There are many services that provide you with a blog. However, they get to store and make use of your content. For those of us that prefer to run our own sites and keep the content under our own control, setting up our own is a better option. In this article I'll cover how to publish your own blog using a static-site generator called Jekyll. We'll walk through getting set up, customizing the layout and, finally, publishing the site. 
## Case study {#case_study} One of my side projects is the Irish shopping website 

[Shop Ireland][2]. It's been a little neglected lately so I thought I'd add something new to the site, and give it more of a voice. The result is the [Shop Ireland News][3]. In this article, Shop Ireland News will be acting as our case study, and you can [download the blog's source code][4] from Github. Feel free to use it as a basis for your own work if you wish. A [link back to my site][5] is always appreciated but not required. 
## Requirements {#requirements} What I wanted from the blog initially was quite simple, though it would be something I hope to expand upon as time goes on. The basic blog would need to have a list of posts, individual post pages, comments, and a way for people to share the posts. At the same time I didn't want to spend a lot of time setting up or managing the blog, so it needed to be very low maintenance. 

## Choosing a platform {#choosing_a_platform} With my requirements in mind, I looked through some of the usual options. 

[Wordpress][6], for instance, is a strong platform with loads of great plugins. However, in my opinion, it was a bit much to consider designing and building a theme in the limited time frame. I could have gone with a pre-built theme, but, in my experience, I usually spend longer taking the theme apart to make it exactly how I'd like it to be. Another prospect was [Jekyll][7]. I have [blogged about using Jekyll][8] before (my site runs it too) and am familiar with how it works. An interesting Jekyll-based alternative is [Octopress][9]. It extends Jekyll to include more plugins and has a nice deploy mechanism for Github. Though, given the limited time I had available, I decided to stick to the simpler Jekyll framework. 
## What is Jekyll? {#what_is_jekyll} Jekyll is a Ruby gem that acts as a local web server on your computer. It's "blog aware," meaning that by default it is set up to help generate a blog. It works by generating a set of static HTML files which can then be hosted anywhere. It's also supported 

[by Github][10], meaning that, if you can push code to Github, you can host your Jekyll site there. 
## Setting Up Your Development Environment {#setting_up_your_development_environment} The first step is to install Jekyll. The full 

[install instructions][11] cover the basics, and if you're using Windows, you can [do it this way][12]. With Jekyll in place, you need a starting template to run Jekyll against. I'm a fan of [Necolas's Jekyll boilerplate][13] but you can also download my [Shop Ireland blog source code][4] if you prefer. To download it, run this in a terminal: 
    git clone git@github.com:donovanh/shopblog.git YourFolderName Note: In order to make this work, you'll need to have 

[git][14] installed. Replace "YourFolderName" with your choice of folder name, and this should copy the source code into the folder. Then run each of the following: 
    cd YourFolderName jekyll serve --watch This will navigate into your folder and tell Jekyll to run a server. Next, browse to to 

`http://localhost:4000` and you should see your local copy of the blog running. 
## Creating Layouts {#creating_layouts} Within your blog directory you should find a 

`_layouts` folder. Within it should be the `default.html` and `post.html` files. These are the layout files that are used to contain the content of your site. 
### Liquid Markup {#liquid_markup} Jekyll makes use of 

[Liquid markup][15] for templates. This is a basic but handy way of adding some logic to the layout files, and allows us to create loops that generate the static HTML files. When setting up a layout, I would usually start by setting up the containing HTML and styling the layout after. The first step is to create the general page layout, and for this you use the default.html file. The `default.html` file acts as a container for your page content. It can include anything that will be on every page, such as the header, sidebar and footer content. It also contains the HTML's head, with its meta tags and CSS files. Within it, you should find a `{{ content }}` marker. This is where individual page content goes. Variables can also be passed in to the layout, and checked like so: 
    <title>{% if page.title %} {{ page.title }} - {% endif %} blog.shopireland.ie</title> In this case, a page can set it's own title value, and it can be accessed within the template using the 

`page` object. 
### Posts Template {#posts_template} With a content container in place, the next step is to set up the blog post template. The 

`post.html` file contains the HTML used by the individual blog post pages. The content from this file is automatically inserted into the `{{ content }}` part of the `default.html` layout file we just finished editing. At the top of this `post.html` file you'll find some text: 
    --- layout: default --- This part of the file is used by Jekyll, and is called 

[frontmatter][16]. It's where you can set any values to be used in the templates. In this case, it's defining the "layout" to be "default". This tells Jekyll to process the file using the default.html layout file. If you have other layout files, changing the name here will direct Jekyll to use that file. 
## Creating a Blog Post {#creating_a_blog_post} Jekyll's blog posts are all stored in the 

`_posts` folder. Creating a new blog post means creating a new markdown file within this folder, and the name of the file dictates both when it will be published as well as the URL (slug) of the post. One advantage of this is that all your blog posts are stored in text files in one place, and not in a database somewhere. They can be easy to back up, and some interesting workflows can be set up including publishing via Dropbox. To see how the posts are created, start by making a new file in the `_posts` folder: 
    2013-08-27-my-great-post.markdown This post is considered to have been published on August 27, 2013, with the URL being something like yourblog.com/my-great-post (this depends on the permalink value in your 

`_config.yml` file). With this set up, you can add in some more information by setting some YAML frontmatter at the top of your blog post: 
    ---
    layout: post
    title: "Your blog post title"
    description: "A description of your post"
    tags: [multiple,tags]
    published: true
    --- This tells Jekyll to use the 

`post.html` layout template and sets some useful meta-data for your blog post. With that in place you can begin writing your post. 
### Markdown {#markdown} Markdown is a popular way to write content without all the usual tags and HTML cruft that converts into HTML. Markdown is quite readible and can be a lot faster to write than writing straight HTML. Lots of examples can be found on the 

[documentation][17], and you can see it in action by browsing the [hop.ie posts][18] directory of my blog. 
## Creating Standalone Pages {#creating_standalone_pages} You can create standalone pages for your site by creating HTML files. If you include the settings text at the top, Jekyll will automatically take the contents of your HTML file and insert into the right template. To show this, open the 

`index.html` file. This file is the home page for the blog, so contains a loop of all the site's posts. It could be any HTML content we wish. Note the frontmatter at the top: 
    ---
    layout: default
    title: Shop Ireland News
    bodyTag: home
    --- This tells Jekyll to use the 

`default.html` file for layout, and show the title as [Shop Ireland News][3]. You can create pages without the ".html" extension. To do so, make a folder and place an `index.html` file into the folder. For example, you could create an about folder with an `index.html` file, and it would be found at yoursite.com/about. 
## CSS Using Sass {#css_using_sass} With the layout in place, your blog will need CSS to style it and make it look better. When writing CSS my preference is to use 

[Sass][19], along with [Compass][20]. Sass is an alternative way of writing CSS that allows the use of variables, predefined functions (or mixins) and lets you write CSS without things like curly braces and semi-colons. Compass is a framework that brings lots of useful mixins to make writing CSS with Sass easier, and can monitor your SASS files automatically. When Compass runs, it monitors your project and will generate CSS based on your Sass files. To do this, Compass looks for a Ruby file, `config.rb`, that tells it which directories to look for your Sass in, and where to put the compiled CSS. If you are using Sass with Compass, you will need to first install [Compass][20], then you can have it monitor your project for changes to the Sass files. Running Compass is then as simple as running a command on your project folder: 
    compass watch There are lots of ways of handling CSS pre-processing, so do feel free to try others and find what suits you best. 

## Building on a Framework {#building_on_a_framework} When building the blog, I needed to get a layout together that would be responsive and easy to extend. With a limited amount of time available, the best course was to use a CSS framework. There are 

[loads of CSS frameworks][21] to choose from. I briefly tried a few but ended up settling on [Bootstrap][22]. It's a framework built by Twitter and very popular. Since I'm using Sass to manage my CSS (rather than LESS, which is the default in Bootstrap), I chose to use [Bootstrap SASS][23]. Looking in the `sass/vendor` folder, you'll find the `bootstrap.scss` file. This file is imported by the `_base.sass` file to bring in the various Bootstrap tools. Along with defining sensible default styling, Bootstrap brings a grid-based layout system that is also responsive. When setting up the blog, I made use of this grid structure to define the columns: 
    <div class="container">
      <section class="row">
        <section class="col-md-8">
          ...
        </section>
        <aside class="col-md-4">
          ...
        </aside>
      </section>
    </div> Bootstrap's column system provides 12 "columns". In this example, I'm setting up a section that is 8 columns wide, and followed by a 4-column wide aside. The 

`row` container ensures that the two elements sit side by side and any further content is placed beneath. The [Getting Started][24] guide is a great place to learn more about what Bootstrap provides. 
## Pagination {#pagination} Jekyll includes some 

[basic pagination][25] that can be used to control how many posts are displayed on a page at a time. The first step in adding pagination is to add a setting to your `_config.yml` file: 
    paginate: 5 This would tell Jekyll to make five pages available at a time. You might need to restart your Jekyll service in terminal, by stopping it (Ctrl+C) and then running it again with 

`jekyll serve --watch`. The `paginate` setting tells Jekyll to make available some information to the liquid templates our pages use. We'll use this to create some previous and next links on the page. Open the `index.html` (the home page) file and add the following where you'd like the links to appear: 
    <!-- Pagination links -->
    <nav class="pagination">
      {% if paginator.previous_page %}
        {% if paginator.previous_page == 1 %}
          <a href="/" class="previous">&laquo;</a>
        {% else %}
          <a href="/page" class="previous">&laquo;</a>
        {% endif %}
      {% endif %}
      {% if paginator.next_page %}
        <a href="/page" class="next ">&raquo;</a>
      {% endif %}
    </nav> This consists of a navigation element that works out whether there's a previous or a next page, and shows the corresponding link as appropriate. It can be used on any page that also includes a loop displaying the blog's posts. The loop code looks like this: 

    {% for post in paginator.posts %}
      ... your post list HTML ...
    {% endfor %} Within this loop you can access any of the posts settings using the 

`post.value` syntax where `value` is the a variable in the frontmatter at the top of the post's file. Find more [information on Jekyll's frontmatter][16]. 
## Comments {#comments} Blogging platforms like 

[Wordpress][6] ship with comment systems built in. Since the blog we're working with is made up of static HTML pages, we don't have the option. However there are some great JavaScript-based services. A popular solution is [Disqus][26]. It's easy to set up, includes full comment administration tools, and best of all, it's free. To get started select "Add Disqus to Your Site", and register your site. When you've completed setup you'll be given a JavaScript snippet. Place it in the posts.html layout template, where you'd like the comments to appear, and you're done. 
## Icons {#icons} With the page content in place for a blog home page, individual blog pages, and on-page styling, it's time to turn our attention to some of the small details that show you really care. Let's make an icon to appear in people's bookmarks and at the top of the browser: the favicon. When making a favicon, it's worth keeping in mind that it's going to be really small. Select something, whether it's your logo, or a symbol that represents it. In my case I chose to put together the letters SI to stand for 

[Shop Ireland][2]. The result is this: [<img class="alignnone size-full wp-image-1176" alt="favicon_example" src="http://flippinawesome.org/wp-content/uploads/2013/10/favicon_example.ico" />][27] It's a `favicon.ico` file and is sized 16 by 16 pixels. To make this, begin with a square canvas in Photoshop or your editor of choice. It's usually easier to start with something bigger than 16 pixels (I would suggest about 500px by 500px), and we'll scale it down later. Once you've assembled an image, the quickest way to generate the favicon itself is to upload it to [Iconifier][28]. It will shrink the file down to 16 pixels and you can then download the resulting .ico file. Place this file in the root of your blog project. With that made, the following line in the head of your HTML will direct the browser to look for it: 
    <link rel="shortcut icon" type="image/x-icon" href="/favicon.ico">

### Other Icons {#other_icons} Happily enough, the Iconifier service will generate more icons for us, including the various "Apple touch icon" icons that appear when someone adds your page to their iPhone homepage. There are multiple variations in file size to allow for the different needs of iPads, iPhones, etc., and all these files can live in the root of your blog. 

## 404 Page {#404_page} We have most of the important pages covered so far, but one that you should always remember is the "Page not found" page. This is the page that is shown when someone mistypes a URL, or perhaps when someone moves content and forgets to put a redirect in place. The purpose of this page is to let your visitor know they've landed somewhere where there isn't content, and help them get to somewhere they'd rather be. To begin, create a 

`404.html` file in the root of your blog project. Jekyll will automatically show this page when any incorrect URLs are hit. The contents of your `404.html` file could look like this: 
    ---
    layout: default
    title: Page not found
    ---
    <article>
      <header id="404">
        <h1>Page missing</h1>
      </header>
      <section class="entry">
       <p>Sorry about that, it seems there's something missing. Please <a href="/">continue to the homepage</a>.</p>
      </section>
    </article> This basic page will let people know what happened. Many choose to do 

[something more fun][29] with their 404 pages, and it's often a chance to be a little more silly than you might get to be on the more public pages. 
## Social media and sharing {#social_media_and_sharing} Depending on your site's goals, you may want to plug in some social sharing and links to social profiles. How you implement this depends on how you want your site to be perceived, so it's up to you how you approach this. On 

[my blog][30], for example, I'm content to have a link to my Twitter profile at the bottom of the page. This is because it's my personal blog and I don't see a need to promote it like a commercial venture. The [Shop Ireland News][3] blog however, is a commercial venture and is better suited to having some obvious social stuff on the pages. The quickest way I have found to do this is to use a service such as [Addthis Smart Layers][31]. To set up Smart Layers, use the Addthis form to set up your chosen social network IDs, and select the way you'd like them presented on the page. It will generate a JavaScript snippet you can paste into your site. In this case, I added the code to the `default.html` layout file as I'd like the social buttons to appear on every page. Simple! 
## Analytics {#analytics} The last thing I would typically do before launching, after doing one last visual check of every page, is add in some 

[Google Analytics][32]. This is a helpful service that lets you see how many visitors you are getting, from where, and when. If you are writing blog posts and want to judge which are working and which aren't, this service will help. To use Analytics, you'll need to register and follow Google's set up instructions for a new account. After setting time zone and naming the account, you'll be given another JavaScript snippet to add to your page. It's up to you whether you want to add the JavaScript to the head or the bottom of your HTML. Keep in mind that JavaScript calls can slow down the rendering of a page, so while putting the tracking in the head might catch the people who leave before the page renders, it might also make the page slower. For this reason I would usually put the JavaScript at the end of the page, before the closing body tag. In this case I'd put it in the `default.html` layout file as the tracking can then note every page view on the site. 
## Going Live {#going_live} With all this in place, we're ready to unleash our creation on the web. To do this, you'll need a domain name. If you haven't chosen one yet, I'd recommend using a service such as 

[Name.com][33] or [Hover][34]. With the domain name chosen, you need a hosting service. Since Jekyll was built by a founder of Github, you can host your Jekyll blog for free with [Github's Pages][35]. This is the option I chose for this project and it seems to work pretty well. Before deploying your site to Github, you'll need to create a `CNAME` file. This file will tell Github what URL you'd like to use. For example, the `CNAME` file for my blog (Hop.ie) [can be seen here][36]. Once you've created this file, we can get all the files ready to send to Github. 
### Pushing Files to Github {#pushing_files_to_github} First, you'll need a GitHub account. Once you've 

[signed up][37] and logged in, select the "Create a new repo" button on the top-right: [<img class="alignnone size-full wp-image-1177" alt="new-repo" src="http://flippinawesome.org/wp-content/uploads/2013/10/new-repo.png" width="203" height="81" />][38] If this is your first time setting up a GitHub page, the easiest way to do so is to call your repo `username.github.com`, where `username` is your chosen GitHub username. Give it a description, I would also usually leave it set to "public". You won't need the `README`. You'll then be given some set up instructions to get your project pushed. Before following them, I'd recommend navigating to your project in a terminal window, and running this command to clear away any existing git stuff: 
    rm -rf ./.git This command will remove the hidden git folder, giving you a clean slate. The instructions from Github will then step through initialising the new Git repo, adding a remote (the place to push the files to), and adding a commit. You'll need to add all the project files to git, so after following the Github instructions, you'll need to run these commands: 

    git add --all
    git commit -am "Adding all the files"
    git push origin master This will add all your files and push them up to the Github repo. 

### Pointing your URL {#pointing_your_url} With the GitHub repo in place, we can tell our web address where to find the site. You'll need to add an "A" record with your domain name registrar. GitHub has full instructions on 

[setting up the DNS][39]. Once the domain name is pointed, you'll may need to wait an hour or two for that to kick in. All things going well, you'll have a live blog! 
## Ongoing Workflow {#ongoing_workflow} As you continue working on your blog, you can push changes up to GitHub and the changes will appear on the site. The git workflow is worth investing a little time into becoming familiar with. Once you are happy with the structure of your blog, adding new posts is then a case of creating a new file in the 

`_posts` folder, adding the frontmatter and your awesome writing, then adding the files with git and pushing them up to GitHub. *This article was originally published as a two-part series at <http://hop.ie/blog/your-own-blog-1/> and <http://hop.ie/blog/your-own-blog-2/>* *Image courtesy of <http://upload.wikimedia.org/wikipedia/commons/thumb/5/51/Closing_in.jpg/1024px-Closing_in.jpg>*

 [1]: /authors/donovan-hutchinson
 [2]: http://www.shopireland.ie/
 [3]: http://blog.shopireland.ie/
 [4]: https://github.com/donovanh/shopblog
 [5]: http://hop.ie/blog
 [6]: http://wordpress.org/
 [7]: http://jekyllrb.com/
 [8]: http://hop.ie/blog/jekyll-github-pages/
 [9]: http://octopress.org/
 [10]: https://help.github.com/articles/using-jekyll-with-pages
 [11]: http://jekyllrb.com/docs/installation/
 [12]: http://www.madhur.co.in/blog/2011/09/01/runningjekyllwindows.html
 [13]: https://github.com/necolas/jekyll-boilerplate
 [14]: http://git-scm.com/
 [15]: http://liquidmarkup.org/
 [16]: http://jekyllrb.com/docs/variables/
 [17]: http://daringfireball.net/projects/markdown/
 [18]: https://github.com/donovanh/donovanh.github.com/tree/master/_posts
 [19]: http://sass-lang.com/
 [20]: http://compass-style.org/
 [21]: http://mashable.com/2013/04/26/css-boilerplates-frameworks/
 [22]: http://getbootstrap.com/
 [23]: https://github.com/thomas-mcdonald/bootstrap-sass
 [24]: http://getbootstrap.com/getting-started/
 [25]: http://jekyllrb.com/docs/pagination/
 [26]: http://disqus.com/
 [27]: http://flippinawesome.org/wp-content/uploads/2013/10/favicon_example.ico
 [28]: http://iconifier.net/
 [29]: http://blog.shopireland.ie/404
 [30]: http://hop.ie/
 [31]: https://www.addthis.com/get/smart-layers
 [32]: http://google.com/analytics
 [33]: http://name.com/
 [34]: https://www.hover.com/
 [35]: http://github.com/pages
 [36]: https://github.com/donovanh/donovanh.github.com/blob/master/CNAME
 [37]: http://github.com/
 [38]: http://flippinawesome.org/wp-content/uploads/2013/10/new-repo.png
 [39]: https://help.github.com/articles/setting-up-a-custom-domain-with-pages