## The egg before the chicken, so vuejs before nuxt... what the heck is vuejs ?

The recent trend is all about template rendering in the browser. No more remote server dumping full page for your browser. Nowadays your browser execute a recipe and literally build the page from the ground up.

Vuejs is a tool helping you to display stuff on the screen.
This is the basic idea.

Imagine you want to show a list of items in a bullet point list, you know you will have to use an `<ul>` and some `<li>` to complete the task... but if you are starting from a blank page, you will have to deal with a bunch of `createElement` and `appendChild` stuff into the body of the page... this is going to be very verbose, and I am just talking about just the first render, imagine now you want to remove some of them, add new ones, or reorder them... who will need a bunch of coffee and a lot of lines of code.

That's why framework like vuejs exists. The idea is pretty old, 10 years ago (the paleolithic age of the web), people had the same problem and starting using JS on the client side to do the job. 

The alternative is to do the job on the server side: you click on a link, you send some variables to the server, the page reload (spoiler: the big deal is here) and you have your fresh page with all the data you want. This is pretty much what is happening on every CMS out there. This is fast, but... you know... you have to wait an extra time before viewing the next page, and the experience is not very smooth !

**Vuejs helps you with a small amount of code to render some data, and find the best solution to update the only part of the page that need to be re-rendered.**

## Thank for the reminder, but this post is all about nuxt right ?

A simple Vuejs app is barrely empty. All you need is a mounting point, a tag on the page where you will *inject* your app.

```
<body>
	<div id="app"></div>
	<script src="app.js" />
</body>
```

This is all you need. This is simple HTML, your browser will deal with the rest, this simple structure is all it need to boot your app... but this is also what search engine bots will see too, and this page holds no data. This is a big deal because most of them will take the code you gave them to extract some meaningful  information. (I know SEO bots are not that dumb, and some of them could run js code to display the page, but for the sake of this article I will assume they only see what you give to them). 

So, this is a pretty bad code if you want to land on the first page of google ! 😭

We need to find a way to display the page with all the data structured by HTML tags. something like

```
<body>
	<div id="app">
		<h1>People I love</h1>
		<ul>
			<li>My kids</li>
			<li>My wife</li>
			<li>Netflix</li>
		</ul>
	</div>
</body>
```

**Nuxt core idea is to give, *whoever asks*, the *final render* of a page and *all the browser needs* to update the page after the first rendering has occured.**

If this is hard to swallow, let me split this in simpler words

* **whoever asks**: for a given URL, your browser, a SEO bot, a facebook linter, or a bash script, they will all received a well formated page with markup.


* **final render**: this output is the last step of maybe some calculation like ordering or whatever data transformation was needed


* **all the browser needs to update the page**: the HTML page will also have some JS instructions to do some extra work once the page is displayed (like ordering a list when a button is pressed)


You can also think like:
I will render a page with all the data to display into (html markup) and the logic to update the layout when I will need to (javascript) the user will not see a blank page very quickly and the data after that, this will be a simple boring HTML page.

Once the page is rendered, you will be able to update the document on the client side (classic vuejs logic)... but rememeber, vuejs will find the best way to update only what has changed, and because a lot of data is already at the right place in the page this is a win win process ! 😎

##### Stop talking and gimme IRL stuff

Let's take a simple exemple: imagine you whant to display a simple list of elements, and once the page is displayed, you want to add at the end of the list a new item. On the server side, nuxt (vuejs under the hood) will send the full page to the browser. Once the page is fully loaded on the client side, vuejs will start and parse the existing markup, be aware of it, and take advantage of this when the new item will be added to the list.

This is awesome because you have both the advantages of the server side and the client side rendering in a single flow 😎


## I make websites for a living, why should I care about all this ?

The main benefice of all this is using vuejs all along.
Nuxt in an extra layer of sugarness, but the real deal is making a whole website only with vuejs. Let's consider using nuxt only for having rendered pages to feed SEO bots. There is a lot of extra stuff you can do with nuxt, but let put them aside for the moment.

When you think about a website, with a CMS or not, you will probably store your data into a database, and build the page with some requests (let's be honnest : a lot) and glue the result with some markup. This is basically what CMS do. Wordpress is a good example of that: data in the db, templating with a theme. Nothing wrong with that — i make a living out of it, I will not throw it away

When you think about an app, you make your mind around a "state". This is a lovely place where all your data are stored. with getter and setter, you data is accessible from anywhere (do you get it ? or do you need a getter ? — joke). This is the parralel idea of the database in our CMS world. The big difference is : the data IS here, no need to make a db request to catch it, if the same data is used on multiple pages : this is a really big time saving.

I know, the data need to come from somewhere... it could not magically appears in your app, at the right place, at the right time !

Nuxt allow you to do your "request my data" job at different "life cycle" levels.

The simpler exemple is the `fetch` function a component could use. This function is only run by the server (more on that later) and allows you to inject into your app store (not the one on your iPhone — joke) the data you just fetched (yes i know, the function name is straightforward).

**reformulation** Imagine you want to show the current weather on every page of your website. When someone request the page from their browser, the nuxt server will request the data from a weather api on the cloud (joke) and feed the store with it. When the page is sent to the user, all the data is delivered in a giant object and vuejs will rehydrate his state from that object (more on that later)

Imagine now, you want to dynamically update the weather depending of the user choice or location instead of a predefined place. The logic is the same : request the weather api, update the store, and update the page (the later part is done by vuejs automatically, thank you bro'). If you follow me this far, you should have noticed the job is already done in the fetch function. Spoiler alert, you can't reuse the function 🤨, but you could (and should) extract the logic from it 😎.

This is where nuxt+vuejs is really good : instead of doing the job twice (first on the server side with PHP/MySQL for example, then on the client side, with Ajax) you share the logic between both sides.

Another example: pagination.
Imagine you have a large list of items, so large you can not fit them into one single page, you have to make chunks from the data, let's say, 10 items a page. The first load will give you the whole page: menu, header, footer and the first 10 items. The second page will give you the same data but the next 10 items.  Why do you need to be given a vast majority of the document you already have (header, footer, menu....) the only think you really want is the next 10 items right ?

In a nuxtjs way of thinking, you will have a function responsible to get the 10 items. On the first load the `fetch` function will be called and you will be able to request the data, inject it into the store and render the full page with the data. The items will be saved on the store (global state of your app) and later if the items on the store change... the UI will be automatically updated.
So the get the next 10 items and so on, you have to request the data to the server (mostly using an API - more on that later) and update the store. The 2 requests (the first one within the `fetch` function and the second on (from next "next result button" for exemple) will share the same logic !
	

## New stack, new rules

I hope your are convined the workflow is very different from what you are used to. There is some caveat you need to be aware of.

### Server
A classic website stack is LAMP (Linux, Apache, MySQL, PHP) — you will find them on every single hosting provider around the world. and because of that a lot of CMS are built around that (wordpress, prestashop etc...). I will assume you have an idea of how things work with that stack.

With nuxt, this is very different, because the server and the client share a lot. Nuxt is made around expressjs (a well known web server/framework in the nodejs community). When you build/compile your app, under the hood a lot of work is done to split your component into express routsw, functions that need to run on the server side, other on the client side etc...

The big deal here, is, your apache server cannot run your vuejs code and magically render a page. this have to be done through the nuxt/express server.

Services like netlify or heroku help you to solve the hosting and deployment problem. But if you feel aventurous, you can start a virtual server from digital ocean, install nginx + nodejs and kick off your app in minutes... but you will still have to manage a lot a stuff (yeah I am talking about security here) you are probably not ready to deal with.


### Data
If i follow on with my previous example of classic CMS, the data is stored on a database, and your website have a way to request it easily. You don't have to write SQL queries to fetch the data, and you can do it wherever you want : no big deal, the entire page is cooked before being sent to the client.

With nuxt you can't execute SQL queries from your code. (okay, maybe you can, but should not). because you server/client code is shared you will probably ends with some credentials exposed in your code (and you do not want that). You will rely on requests such as REST or GraphQL APIs. At first sight, this could not appears to be a smart choice (to split thing apart), but you will have some benefits like, separation of concerns, more scaling options for backend/frontend parts of your app.

This is the way data are consummed: through API. Wordpress has a builtin REST API that exposes data, ready to be consumed by JS script (guttenberg is a pretty good example of that - strict separation between the data blocks and the UI)

Also, if you expose your own data throught api, this will be easy to test with unit tests, and easy to mock when you work on the render.


## What now ?
I have built a couple of websites using nuxt now and I have really loved it. I encourage everyone to try to build a little something with it to forge your own opinion. Sure, haters will always find something to say, and nuxt is not for everyone — I got it.

I highly recommand to give it a try. Nuxt is very powerfull. As a lot of good intentions wrap its core, you will feel at ease rapidly. Sure, if you already know vuejs (even a little bit of) you will be at ease with nuxt, but the tree structure of a default nuxt project is simple enough to help you write a couple of page and component.






 


