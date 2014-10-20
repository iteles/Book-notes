Examples of code in these notes are, as a general rule, copied from the book directly as these proved very apt at explaining the surrounding wording.

#Table of Contents
* [](#chapter1)
* [](#chapter2)
* [#Chapter 3 - Developing in AngularJS](#chapter3)

<a name="chapter1"/>
#Chapter 1 - Introduction to AngularJS

###Interesting points to note:
* With Angular the template for the page and the data get shipped to the browser and assembled there
  * The role of the server is therefore only to serve static resources (like images) and handle the data, no assembly of pages required
* Functions receive a $scope object that does not need to be manually created
* Because of the $scope object, there is no need to register event listeners or have IDs and classes to hook event listeners to
* Angular uses an MVC application structure
* Angular uses _data binding_ which allows us to map Javascript properties to specific parts of the view and have them sync automatically
  * Double curly brackets ``{{}}`` are used for data binding
* The $scope object that does the data binding is passed automatically - you just ask for it in the javascript when you write the constructor; you can ask for a number of things when you do this
   * This is _dependency injection_ where classes just ask for what they need rather than creating dependencies
* Angular also comes with a lot of attributes that aren't part of the standard HTML specification like the ``ng-controller`` and ``ng-model`` tags - these HTML extensions are named _directives_
  * You can also write your own directives

###Model-View-Controller (MVC)
The key idea with the MVC application structure is a separation of data (model), the application logic (controller) and what the user sees on their screen (view).

###A few bits and pieces:
* The `ng-app` tag tells Angular how much of the page you want it to manage, so if you use `<html ng-app>`, it means you want Angular to manage the whole page
  * This is useful if you're integrating Angular with an existing app that uses other methods and languages
* Javascript classes called _controllers_ are used to manage areas of the page - these are also the name of the function in your js file, e.g. `<body ng-controller='CartController'>`
* The `ng-model` tag is used to keep changes in sync as it refreshes data when it is updated

<a name="chapter2"/>
#Chapter 2 - Anatomy of an Angular Application

To invoke Angular you just need to **load the Angular.js library** and **tell Angular which part of the DOM is should manage using the ng-app tag**.

The script can be loaded using Google's [CDN](http://en.wikipedia.org/wiki/Content_delivery_network):
```javascript
<script src="https://ajax.googleapis.com/ajax/libs/angularjs/1.0.4/angular.min.js">
</script>
```

* Put simply, the **model** includes the standard variables you create like strings and arrays, the **views** are the HTML templates with things in {{curly brackets}} for data binding and the **controllers** are the Javascript classes you write to control the relationship between you model and your views

* The right way to **define a controller** is as part of a **module** in your js file so that you're keeping it _out of the global namespace_

Angular event handlers are different from the standard Javascript handlers in that they _do not operate in the global namespace_ and Angular deals with cross-browser differences for you.
* The `ng-bind` directive is used to update text - it has two equivalent forms: `<p ng-bind="greeting"></p>` and `<p>{{greeting}}</p>`
  * **For the index.html page _always_ use ng-bind for your data binding**, otherwise the user might see the HTML page without your data first; everywhere else you can use the {{curly brackets}} syntax
* To keep fields updated no matter how they updated (via server or user input), use the $scope function `$watch()`
  * You call `$watch()` first with the expression to observe and then the callback which is called when the expression changes
  * `$scope.$watch('funding.startingEstimate', computeNeeded); //watch the expression 'funding.startingEstimate and call computeNeeded() when it changes'` - note the expression to watch is in quotes
* `ng-repeat` allows you to iterate through an array and also gives you a reference to your location within the array through the use of `$index` (also $first, $middle, and $last)
* You can use `ng-submit` to call a function when a form submits
  * You would use `ng-submit` on the form element itself and it also _keeps the browser from carrying out the default POST action on submit_
* Other event-handling directives include `ng-click` (instead of 'onclick') and `ng-dblclick`, etc
  * Note (from separate source): `ng-click` only works with mouse click so if you want something to happen when a use hits the enter key at the end of a form, try to use `ng-submit` on the form element instead
```javascript
//using ng-click for example within a form would look like this:
<button ng-click="reset()">Reset</button>

//the javascript would then look like this:
$scope.reset = function() {
  $scope.parameterToReset = 0;
}
```
* `ng-show` and `ng-hide` function by setting the CSS element styles to `display:block` and `display:none`
* `ng-class` can be used to conditionally create a class name (for example, when a button is cliked) which then changes the styling of that element via CSS
```javascript
<table ng-controller='RestaurantTableController'>
  //where ng-class add the class 'selected' to <tr> element based on the result of $index==selectedRow
  <tr ng-repeat='restaurant in directory' ng-click='selectRestaurant($index)' ng-class='{selected: $index==selectedRow}'>
    <td>{{restaurant.name}}</td>
    <td>{{restaurant.cuisine}}</td>
  </tr> </table>

//in the javascript we would have:
function RestaurantTableController($scope) {
//dummy data for the purposes of this example
$scope.directory = [{name:'The Handsome Heifer', cuisine:'BBQ'},
                    {name:'Greens Green Greens', cuisine:'Salads'},
                    {name:'House of Fine Fish', cuisine:'Seafood'}];

//assigns the row that is clicked on to $scope.selectedRow (which is then compared to the $index)
$scope.selectRestaurant = function(row) {
  $scope.selectedRow = row;};
}

//and in the CSS
.selected {
background-color: lightgreen;
}
```
* For images and URLs you should use `ng-src` and `ng-href` and you can then use the {{curly brackets}} for data binding as usual
* You can also do simple math or comparisons in templates but I wouldn't unless absolutely necessary to keep the separations intact

>Controllers have three responsibilities in your app:
>• Set up the initial state in your application’s model
>• Expose model and functions to the view (UI template) through $scope
>• Watch other parts of the model for changes and take action

###Controllers
* Use _one controller per function_ in your view to keep them small and manageable
* When using nested controllers, the $scope passed to the child controller has access to all the properties of $scope passed to the parent controller

###Data
* Angular only considers data to be part of the _model_ when it can be accessed through the $scope
* `$scope` can be defined in 3 ways:
  * Through the standard format we've seen until now, in the javascript
  * Through expressions such as `<button ng-click='count=3'>Set count to three</button>`
  * Through using `ng-model` on a form

###$watch()
The `$watch()` function is possibly the most used scope function: `$watch(watchFunction, watchAction, deepWatch)`
- _watchFunction_ is an Angular expression (passed in as a 'string') or function that is being watched for changes
- _watchAction_ is an Angular expression or function that is carried out when the _watchFunction_ changes
- If _deepWatch_ is set to true it examines each object within the _watchFunction_ (so if the watchFunction is an array, it walks all the properties for the array)

Using the `dreg();` function after the `$watch()` function de-registers the event listener

The most performance-efficient way of using `$watch()` can sometimes be to set up a watch run every time Angular evaluates the page (avoiding expensive data binding where certain functions are run multiple times)

To **watch multiple items** you can either put them in an array (and pass in `deepWatch` as true in your `$watch()` function) or pass in a concatenated set of properties, for example to watch all the values in the things array ` $scope.$watch('things', callMe(...), true);` (setting deepWatch to true then means that any changes to any items in the _things_ array will trigger the _callMe()_ function)

###Modules
In large 'real world' apps, putting functions in controllers quickly becomes un-manageable. To avoid the controllers becoming a dumping ground for anything we need, we use **modules** instead:
* Provide a way to group dependencies for a functional area within your app
* Avoids the problem of duplicating code when multiple controllers need to access the same information on the server
* Automatically resolve dependencies (through dependency injection) so there is no need too run an actual server (which would cause a performance issue) or patch in dummy data for unit testing

Modules mean your code can look like this:
```javascript
function ShoppingController($scope, Items) {
      $scope.items = Items.query();
    }
```
Instead of like this:
```javascript
function ItemsViewController($scope) {
      // make request to server
      // parse response into Item objects
      // set Items array on $scope so the view can display it
    }
```
But what is the `Items` object passed to the module example above? It's a **service**.
>Services are singleton (single-instance) objects that carry out the tasks necessary to support your application’s functionality.
Angular comes with in-built services like $local for inter-acting with the browser's location or $route for changing the view based on URLs, but _you should also write services to carry out the functionality that is unique to your app_. They can be shared across controllers and are a **great tool for sharing state**.

The parameters passed in as strings are also order-independent.

###Data filters
Data filters allow you to define how to transform your data from within the template - the key thought process here is that you would do this within your view (instead of your controller or model) because it's for things that are only important when displaying the data to humans but make no difference to the logic in your controller (like adding a dollar sign for currency for example).
Format: `{{angularExpression | filterName : parameter1 : parameter2 ... | filterName2 ...}}`

You can add more than one data filter to an expression by adding pipe symbols in the binding.

You can also easily create your own filters using the format:
```javascript
var homeModule = angular.module('HomeModule', []); homeModule.filter('titleCase', function() {
var titleCaseFilter = function(input) {
￼//javascript code
};
return titleCaseFilter; });

//the view would be as follows
<body ng-app='HomeModule' ng-controller="HomeController">
  <h1>{{pageHeading | titleCase}}</h1>
</body>
```
The `pageHeading` would be inserted via a controller in this heading.

###Changing View with Routes & $location
Angular's `$route` service manages which views are shows based on the URL that is requested. Note: Whilst trialling this out, I found a few additional pieces of information which I have added to the [Route](#routes) section of my Lessons Learned below.
```javascript
//A small code snippet from my own simple code to illustrate
var appModule = angular.module('allTheHellos',['ngRoute']);

appModule.config([ '$routeProvider', function($routeProvider) {
  $routeProvider
    .when("/",{
      templateUrl: 'home.html'
    })
    .when('/view-profile/:id',{
      controller: 'FriendInfoController',
      templateUrl: 'view-profile.html'
    })
    .otherwise({
      templateUrl: "404.html"
    });
}]);

appModule.controller("FriendInfoController", function($scope, Friendlist, $routeParams) {
    $scope.friend = Friendlist[$routeParams.id];
});
```
**To talk to servers** you would use Angular's `$http` service, which will be discussed in [Chapter 5](#chapter5).

###Directives
Directives extend the HTML syntax and Angular has many inbuilt examples of these such as `ng-repeat` and `ng-show`. They will be covered in detail in [Chapter 6](#chapter6) but simply put, the overall structure of creating custom directives for when Angular's ones don't do what you need them to is `modulename.directive('directivename', directivefunction(){...});`

###Forms
* Angular has some built-in form extensions such as `ng-maxlength` and `ng-minlength` for form input fields
* Using HTML5 attributes such as adding the attribute `required` to an input field is totally supported and will simply be replaced by Angular with directives
* Angular already has some built-in validation whenever the `<form>` tag is used
  * Angular will set a `$valid` property to _true_ when the input is valid - this property can be accessed through a controller once it has been added to the `<form>` element
  * Or, for example, we can prevent the form from being submitted if it's not valid by using `<button ngDisabled=!addUserForm.$valid>Submit</button>` where 'addUserForm' is the form _name_

<a name="chapter3"/>
#Chapter 3 - Developing in AngularJS
The aim of this chapter is to provide a **very high level view of how to lay out an app** along with some tool and workflow tips; the details of building a sample application will be covered in [Chapter 4](#chapter4)

**Suggested folder structure** for an Angular app:
* **app** - contains all of the code for what is displayed to the user _(note: I've also seen this referred to as 'public')_
  * **/styles** - contains all CSS files
  * **/images**
  * **/scripts** - contains the majority of the Angular code
    * **/controllers**
    * **/directives**
    * **/filters**
    * **/services**
  * **/vendor** - contains all the libraries your app depends on, including AngularJS
* **/config** - contains Karma configurations for testing
* **/test** contains files for unit and scenario testing
  * **/spec** - contains files for unit tests, mirrors the structure of the scripts folder in the app directory
  * **/e2e** - contains the end to end scenario testing files

* It's **good practice** to use the _array-style injection_ as per the below as this will avoid bugs later when you start compiling your code
```javascript
myAppModule.controller ('MyControllerName',['$scope', 'otherDependencyName', function($scope, otherDependencyName){
  //code here to do something awesome
}]);
```


#My Own Lessons Learned
I added this section to capture a few tips and things I picked up that weren't explicit in the book.
* When learning, always use a CDN for an **uncompressed** library - the console error messages will be way more helpful than with a minified version
<a name="routes"/>**Changing view with Routes**
* `ngRoute` has been taken out of the main angularJS library and now needs to be loaded into your html via a separate `.../1.2.26/angular-route.js` script (at the time of writing 1.2.26 is the latest stable release)
Full list of steps:
1. You should have an index.html file (or similar) which includes all the code that will be repeated in every page of your app (e.g. Header with navigation menu) and then a div (or similar) which will contain the various views; these views are essentially snippets of HTML that fill out the body of your app
2. Add `ng-view` to the div in your index.html which will contain your views
3. Add the `ngRoute` CDN to your script tags in index.html (as per bullet point above)
4. Add `ngRoute` as a dependency in your module
5. Create your `someModule.config(function($routeProvider){})` configuration block, calling `$routeProvider` as a service
6. Angular by default adds hashes (#) to your URL, e.g. .../#/about.html - to stop this from happening, add `$locationProvider.html5Mode(true);` to your configuration block (_NOTE: this implies adding $locationProvider as a dependency in the block as well like so: `appModule.config([ '$routeProvider', '$locationProvider', function($routeProvider, $locationProvider){}`_) and `<base href="/">` to the <head> of your index.html file
7. Due to browser security restrictions, when trying the code out yourself, it won't run as just file:// in the browser, you'll need to run a simple server - I found the easiest way to do this was installing node static with `npm install -g node static` and then running it by calling `static` from the terminal whilst inside the same directory as my app
