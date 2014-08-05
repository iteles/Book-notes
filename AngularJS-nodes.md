Examples of code in these notes are, as a general rule, copied from the book directly as these proved very apt at explaining the surrounding wording.

#Chapter 1 - Introduction to AngularJS

Interesting points to note:
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

#####Model-View-Controller (MVC)
The key idea with the MVC application structure is a separation of data (model), the application logic (controller) and what the user sees on their screen (view).

A few bits and pieces:
* The `ng-app` tag tells Angular how much of the page you want it to manage, so if you use `<html ng-app>`, it means you want Angular to manage the whole page
  * This si useful if you're integrating Angular with an existing app that uses other methods and languages
* Javascript classes called _controllers_ are used to manage areas of the page - these are also the name of the function in your js file, e.g. `<body ng-controller='CartController'>`
* The `ng-model` tag is used to keep changes in sync as it refreshes data when it is updated

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
  * You would use `ng-submit` on the form element itself and it also keeps the browser from carrying out the default POST action on submit
* Other event-handling directives include `ng-click` (instead of 'onclick') and `ng-dblclick`, etc
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

**Controllers**
* Use _one controller per function_ in your view to keep them small and manageable
* When using nested controllers, the $scope passed to the child controller has access to all the properties of $scope passed to the parent controller

**Data**
* Angular only considers data to be part of the _model_ when it can be accessed through the $scope
* `$scope` can be defined in 3 ways:
  * Through the standard format we've seen until now, in the javascript
  * Through expressions such as `<button ng-click='count=3'>Set count to three</button>`
  * Through using `ng-model` on a form

**$watch()**
The `$watch()` function is possibly the most used scope function: `$watch(watchFunction, watchAction, deepWatch)`
- _watchFunction_ is an Angular expression (passed in as a 'string') or function that is being watched for changes
- _watchAction_ is an Angular expression or function that is carried out when the _watchFunction_ changes
- If _deepWatch_ is set to true it examines each object within the _watchFunction_ (so if the watchFunction is an array, it walks all the properties for the array)

Using the `dreg();` function after the `$watch()` function de-registers the event listener

The most performance-efficient way of using `$watch()` can sometimes be to set up a watch run every time Angular evaluates the page (avoiding expensive data binding where certain functions are run multiple times)

To **watch multiple items** you can either put them in an array (and pass in `deepWatch` as true in your `$watch()` function) or pass in a concatenated set of properties, for example to watch all the values in the things array ` $scope.$watch('things', callMe(...), true);` (setting deepWatch to true then means that any changes to any items in the _things_ array will trigger the _callMe()_ function)
