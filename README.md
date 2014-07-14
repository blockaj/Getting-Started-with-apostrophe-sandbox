#Getting Started with apostrophe-sandbox

apostrophe-sandbox is an apostrophe server that has everything you need, where you need to get started quickly. Learn how to make you own apostrophe site from the beginning.

##Setup
1.	You can download apostrophe-sandbox from Github by typing into your terminal:

		git clone https://github.com/punkave/apostrophe-sandbox.git 
2.	Once downloaded you can `cd` into the directory where you downloaded apostrophe-sandbox and type `npm install`. 
3. Once you're done installing all of the modules, you can open your project. At the top, you should see something that looks like this:

  		root: module,
  		shortName: 'apostrophe-sandbox',
  		hostName: 'apostrophe-sandbox',
  		title: 'Apostrophe Sandbox',
  		sessionSecret: 'apostrophe sandbox demo party',
  		adminPassword: 'demo',
 *	You can leave `root: module` as it is but you'll want to change `shorName` and `hostName`. These can be whatever you want. They're there to specify what database your apostrophe site accesses. 
 * `sessionSecret` can be whatever you want. Just keep in mind that it's there for security purposes so if it's complicated, that's okay. *
 * Lastly, you should change `adminPassword`. This is the password you will use to log in to your apostrophe site. 

4.	With all this basic setup done, make your way down to the part of `app.js` that looks like this: 

		pages: {
    		types: [
      			{ name: 'default', label: 'Default (Two Column)' },
      			{ name: 'onecolumn', label: 'One Column' },
     		 	{ name: 'blocks', label: 'Blocks' },
      			{ name: 'marquee', label: 'Marquee' },
      			{ name: 'home', label: 'Home Page' },
      			{ name: 'blog', label: 'Blog' },
      			{ name: 'map', label: 'Map' },
      			{ name: 'groups', label: 'Directory' },
      			{ name: 'company', label: 'Company' }
    		]
  		},
As you may have guessed, this is where you specify all of the different page templates in apostrophe. The `name` is the name of the html file stored in `view/pages/`. Here, you can delete everything except for the home template that looks like `{ name: 'home', label: 'Home Page' }`.

5.	Now that you've deleted all of the templates in `app.js` you need to delete all of them in the apostrophe-sandbox directory. Go into the `views/pages` directory in apostrophe-sandbox. You can delete the following files:
 *	 blocks.html
 *	 company.html
 *	 default.html
 *	 marquee.html 
 *	 onecolumn.html
 *	 sections.html

6. Finally, go into `public/css`. You can also delete the following files:
 *	blog.less
 * homepage.less
 * defaults.less
 * fonts.less
 * global.less
 * lockups.less
 * login.less
 * map.less
 * responsive.less
 * search.less
 * sections.less
 * templates.less

Now, you're ready to start working on the html and css for your apostrophe site.  

##Gutting the pages
If you run `node app` in your terminal now and try to log on to your site, you should get some errors. These are telling you that you're missing some files specified in `site.less`. `site.less` takes all of your css or less files and combines them into one, big css file. To fix this error, go into your `site.less` file and delete all of the lines that `@import` the less files that we deleted above. 

Now take a look at the `layout.html` file in `views/global`. It looks like there's a lot going on in here but you can delete practically all of it. You should see  `{% block header %}`. You can delete everything inside this block. Make sure not to delete the `{% endblock %}` that corresponds with our header block. 

You can delete the breadcrumbs block all together if you want, even the block itself. You should delete everything inside `{% block main %}` just like we did with the header block. Again, don't delete the blog itself, just the content inside. 

You can delete the extra content block all together. You can delete everything inside `{% block footerBody %}`. You could leave `{% block footer %}` and delete everything inside of it. It's just a matter of how you want to organize your template. You can also add or delete blocks later if you need to. 

At this point, you should just see a collection of nunjucks blocks in your `layout.html`. Now, we can go over what all of these sections are. 

Later, when you go into `home.html`, you'll see that the first line is a nunjucks line: `{% extends 'layout.html' %}`. As you may already know, this means that `home.html` can access all of the nunjucks blocks inside of `layout.html`. Let's say you want a header that is common to all pages on your apostrophe site. If you have all of your templates extend layout, you can do that really easily. Now let's say you want to have the same header on all of your pages except for the homepage. You still have all of the pages (including `home.html`) extend `layout.html`. You would put `{% block header %}` in your `home.html` to override whatever the header is in `layout.html`.  Using these blocks is a way of easily managing content across all of your pages. 

Now, go into `home.html` and delete all of the content inside the hero and mainContent blocks. 

##Adding content
Now lets create a simple homepage. Let's have the header on the homepage be different from every other page. As I said before, this means we'll override the header block in `layout.html` by adding a header block of our own inside `home.html`. I've created the beginnings of my homepage header:

	{% block header%}
 		<div class="header">
    		<h1>{{ aposArea(page, 'siteTitle') }}</h1>
    		<div class="nav">
     	 		<ul>
        			<li>
        			</li> 
      			</ul>
    		</div>
  		</div>
	{% endblock %}
I'm sure the thing that stuck out to you and possible confused you was `<h1>{{ aposArea(page, 'siteTitle') }}</h1>`. `aposArea` is apostrophe for regular text. It will be editable when you log in to your apostrophe site. We also passed two parameters to this nunjucks function: `page` and `siteTitle`. `siteTitle` is the name of the `aposArea`. By giving it a name it won't get confused with any other `aposArea` so that text you put in one `aposArea` won't show up in another. The name can be whatever you want but considering that this piece of text was going to hold the site title, it felt appropriate that I call the `aposArea` siteTitle. The `page` parameter means that this text is specific to the page it's on. In other words, if you were to make an `aposArea(page, 'siteTitle')` on a different page, they won't get confused. 

Now, let's start up our site to make sure the changes we've made are reflected in the browser. 
!["Our apostrophe site so far."](apostrophe-site1.png "Apostrophe Site")
I know, that doesn't look like much of a header for our site, but we'll style it soon. Now let's work on our navbar.
	
	<div class="nav">
      <ul>
        <li><a href="/">Home</a></li> 
        {% for tab in page.tabs %}
          <li><a href="{{tab.slug}}">{{tab.title}}</a></li>
        {% endfor %}
      </ul>
    </div>
In a apostrophe, we've got an object called page with a property called tabs. We can loop through `page.tabs` like I did above with some nunjucks to list all of pages in the navbar. The problem is that all pages are children of the home page by default. This means that the home page won't be accounted for in page.tabs, which is why I added it above. 

Now we've got a really basic header. Let's start styling it. 

##Adding style 
Now we can style our header a little bit. We don't have a less file for our homepage yet, so first, let's create one. In `public/css` I added a file called `home.less`. In order for our new less file to work, we need to add `@import 'home.less;` into our `site.less` file. Now we can add to `home.less`. 

	.header {
		margin: 5%;
		h1 {
			font-size: 5em;
			font-weight: 700;
			font-family: sans-serif;
		}
		.nav {
			ul {
				li {
					display: inline-block;
					a {
						font-size: 1.3em;
						font-family: sans-serif;
						color: black;
						font-weight: 600;
						text-decoration: none;
						line-height: 1.3em;
						&:hover {
							text-decoration: underline;
						}
					}
				}
			}
		}
	}
Now are page looks like this:
!["Look at our new apostrophe site!"](apostrophe-site2.png)

