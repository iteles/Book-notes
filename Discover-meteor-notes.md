Ahead of a [Meteor London](http://www.meteorlondon.com) workshop, I decided to make my way through [Discover Meteor](https://book.discovermeteor.com/) to make sure I understood the core ideas and wasn't rushing to catch up all day!
Below are some notes I made throughout, with some of my own notes sprinkled in (from research carried out where I wanted further clarification on a point).
All examples are taken from this excellent book, unless otherwise stated.

###Setup
* `meteor add mizzao:bootstrap-3` adds the Bootstrap framework
  * This could be added as usual by including its files in our project but this way the package keeps it up to date
* `meteor add underscore` adds [underscore.js](http://underscorejs.org/), a javascript utility library

* Start your meteor app by typing `meteor create <appname>` and you'll get some scaffolding including a `.meteor` folder which holds all of meteor's own code - don't mess with this folder!  
* Suggested app structure (there are no mandatory structures in meteor):
  * **server** - only runs on the server
  * **client** - `main.js` and `main.html` will be in this folder, which only runs on the client
    * `main.*` files are loaded **after everything else**
    * **CSS goes in the _client_ directory** (not in the _public_ one) because it is minified and loaded by meteor
    * Meteor is pretty impressive at finding your client files so the structure inside this folder doesn't matter - use as many sub-folders as you like to keep it clean and organised
  * **public** - contains your _static assets_ like images, favicons and fonts
    * When referencing files in this folder there is no need to use _public/_, just _/<asset-name>_
  * **lib** - is loaded **before anything else**
    * Collections will be kept in this folder as they will need to be accessed from both server and client
  * ** test**
* Everything outside the _server_ and _client_ directories run on **both** server and client
* Note: To write in [CoffeeScript](http://coffeescript.org/) rather than vanilla JavaScript, just make sure you add the CoffeeScript package to your app `meteor add coffeescript`

###Templates
* Meteor's templating engine is [Spacebars](https://github.com/meteor/meteor/blob/devel/packages/spacebars/README.md)
* To _include_ the template in your HTML use the syntax `{{ > templateName}}` - these are called **inclusions**
* The name of the actual _file_ of your template isn't relevant, it's providing a `name` in the opening `<template>` tag, e.g. `<template name="templateName">` that meteor looks for
* **Expressions** are in the format `{{title}}` return a property of the current object or the value of a template helper
* **Block helpers** control parts of the template, e.g. `{{#each}}…{{/each}}` to iterate over objects or `{{#if}}…{{/if}}` to add things in conditionally

* Templates and their logic are kept separate in Meteor, with the logic in _template helpers_
  * Examples: `post_item.js` will hold the logic for the `postItem` template

* As templates iterate through datasets with `{{#each}}…{{/each}}`, they assign each item in that array to `this` in turn, for example in our array of posts, `this` will be assigned to each post in turn so `this.url` will always refer to the `url` property of the **current** post that is being iterated over - this means that in the template helpers we can safely use `this.property` to refer to a property of our current item
An example of a common but simple pattern used in template helpers:
```javascript
//holds postItem template's logic
//returns {{domain}} and {{url}} for postItem template
Template.postItem.helpers({
  domain: function() {
    var a = document.createElement('a');
    a.href = this.url;  //returns full URL, e.g. http://sachagreif.com/introducing-telescope/
    return a.hostname; //returns just domain, e.g sachagreif.com
    //essentially domain = a.hostname & a.href=this.url
  }
});
```

###Collections
* Collections are how Meteor keep data synchronised between the front and back end
* They **store persistent data** by taking care of storing it in the permanent server-side and also provide access to all client browsers in realtime _without having to write a lot of server side code_
* Collections live in the **/lib** folder to make sure they're always loaded first
  * Also remember that because the _/lib_ folder is outside of the _/client_ and _/server_ folders, it will run on _both client and server_
```javascript
//creates a new collection named 'posts'
Posts = new Mongo.collection('posts');
```
* [MongoDB](http://www.mongodb.org/) is the default server-side database for Meteor
  * To **look inside your database**, open a separate terminal window while your meteor app is running, type `meteor mongo`
  * This brings up the [Mongo Shell](http://docs.mongodb.org/v2.2/mongo/) which you can use with commands like
* The collection **acts as an API** to your Mongo database, so you can write things like `Posts.insert()` in your server-side code

**Client-side collections**
>Collections get more interesting client-side. When you declare `Posts = new Mongo.Collection('posts');` on the client, what you are creating is a local, in-browser cache of the real Mongo collection. When we talk about a client-side collection being a "cache", we mean it in the sense that it contains a subset of your data, and offers very quick access to this data.

* **MiniMongo** is Meteor's client-side implementation of Mongo
* You don't send your _whole_ database to the client so a **client-side collection** consists of a subset of your database **stored in browser memory**, allowing _almost instant_ access
* Meteor synchronises the client and server-side data through the _collection name_ ('posts' in the example above)
* To test this is all working, you can insert items from the _brower console_, so in the example of our 'posts' collection, this might be `db.posts.insert({ title: 'Introducing Telescope', url: 'http://sachagreif.com/introducing-telescope/'});`

**Dynamic Data**
* Meteor does all the synchronising between client and server for you, so all you need to do to bind your collection data to your template is use your template helper - in this case we're using the _postsList_ template helper for the `<template name=postsLists>` template (it all fits together nicely really)
```javascript
Template.postsList.helpers({
  posts: function(){
    return Posts.find();
  }
});
```
* `find()` returns a cursor and doesn't immediately access the database or return results
  * `find()` is also a _reactive data source_ which means that changes to the collection it points to will trigger a recomputation, although this can be switched off with `{reactive: false}`
* Using `fetch()` transforms it into an array of the actual objects though, e.g. `Posts.find().fetch()`
> The server-side collection pulled the posts from Mongo, passed them over the wire to our client-side collection, and our Spacebars helper passed them into the template.

###Publishing & Subscribing
Using `find()` assumes that the whole server-side db will be on the client. **This is possible because up until now we've had the `autopublish` package switched on, which mirrors your server-side database to all your clients - this _should not be on_ for production apps as it's a security risk.** Using _publish & subscribe_ allows you to filter the data shared with clients** - it takes a subset of your database and shares it to the client.
* To remove autopublish type `meteor remove autopublish` in your terminal
* Now we need to choose what to share from the server with the client:
  * We **publish from the server** using a _server/publications.js_ file:
  ```javascript
  Meteor.publish ('posts', function(optionalParameters){
    return //whatever we want to share with the client goes here
  });
  ```
  * We **subscribe to the publications on the client** - best practice is actually _subscribe using a [`waitOn`](#waiton)_ in the _router.js_ file:
  ```javascript
  Meteor.subscribe('posts', 'optionalParametersToPassIn');
  ```
* Any data you subscribe to is _mirrored_ on the client through MiniMongo
_Terminology aside:_ This data is transferred using **DDP** ([Distributed Data Protocol](http://www.eventedmind.com/posts/meteor-subscriptions-and-ddp)).
This shared access to the database from both client and server is Meteor's principle of **database everywhere**.
* Meteor **sends raw data** to the client rather than HTML and it's the client that renders it, which means you can access data & modify it almost instantaneously

* Various levels of publishing:
  * Publish all the datan in your collection (doesn't make much sense unless you're right at the beginning of developing), using `return Collection.find()` in your `Meteor.publish` function
  * Publish a subset of your collection, using `return Collection.find({'property: value'})` in the publish function
  * Publish only some of the properties of your collection data, using `return Collection.find({}, {fields: {property:value}});` in your publish function, e.g. `return Posts.find({'author':'bob'}, {fields: {date:false}})` will return all the posts written by Bob and **exclude** any data on the date they were written (remember this is not a conditional statement, it will just return all the data on bob's posts **except** the date data)

* To **display** only a subset of the data you have subscribed to (the example in the book is finding all posts by one author - loading them all into memory - but only wanting to display a specific category at this time), you use `find()` in your template helper:
```javascript
Template.posts.helpers({
    posts: function(){
        return Posts.find({author: 'bob-smith', category: 'JavaScript'});
    }
});
```
###Routing
_Routing_ looks at what the URL is that you're requesting and changes the content displayed in the browser. In Meteor **routing is done with [Iron Router](https://github.com/EventedMind/iron-router)** - `meteor add iron:router` to install.

You'll want to alter your HTML page so that:
* `main.html` contains only the <head> of your document, body is moved out to your layout template
* `layout.html` is a new template (suggest this is kept in **client/templates/application**) which is essentially the `<body>` of your page
  * It contains any standard content that is to be displayed on every page (a navigation bar for example)
  * Don't forget to replace the `<body>` tags with `<template name="layout">`
* The portion of the content inside `layout.html` that should be replaced by a template based on the routing should be denoted by `{{> yield}}`
  * `yield` is a _special template helper_ in Iron Router and acts as a placeholder for content
  * More (and quite interesting) [info on `yield` can be found in the Iron Router guide](https://github.com/EventedMind/iron-router/blob/devel/Guide.md#layouts)

Your `router.js` file will **contain your routes** - it is recommended this is kept in the **lib** directory so it's loaded first
```javascript
//Example lib/router.js file
Router.configure(
  //defines a default layout template for all routes using the global (configure) router option
  { layoutTemplate: 'layout'  }
);

//when the home directory is requested, the postsList template is displayed
Router.route('/',
  { name: 'postsList' }
);
```
Meteor will automatically look for a template with the same name as the route ('postsList' in the example above).  
_Aside:_ Because we have named our route in the routing above, we can also use the `{{pathfor}}` spacebars helper which keep us from having to hard-code links into our HTML: `<a class="navbar-brand" href="{{pathFor 'postsList'}}">Microscope</a>`

<a name="waitOn">
**Using `waitOn` to avoid blank loading time**
* The `waitOn` function used in the router allows you to wait until the function is 'ready' deliver the data to the browser
  * In the meantime, Iron Router supports the use of a `loadingTemplate` which it will display whilst the data is being loaded
```javascript
//these two lines would be added to your Router settings
//if added to Router.configure, this happens once at the first loading of the app
loadingTemplate: 'loading',
waitOn: function() {
  return Meteor.subscribe('posts');
}
```

**Routing to specific items**
This is for example when an ID is entered after the root in the URL and only the posts with that particular ID is shown:
```javascript
Router.route('/posts/:_id',
//the '_id' here goes into the router's params array
  //postPage template will be called within the data context of the post we have asked for via the ID
  {name: 'postPage',
    //this data context passes the id in the current URL (this.params._id) to the findOne function
    data: function(){ return Posts.findOne(this.params._id);}
   }
);
```
Note the **data context** in the code above.






###Further Notes
* In Meteor, the `var` keyword limits the scope of the variable to the current file so you wouldn't use it for collections you want to be available for your whole app, e.g.
```javascript
Posts = new Mongo.collection('posts');
```
* You can get your app's log by typing `meteor logs myappname` into the terminal
ZZZ * client/main.html:1: Can't set DOCTYPE here.  (Meteor sets <!DOCTYPE html> for you)
