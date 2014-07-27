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

* The right way to **define a controller** is as part of a **module** in your js file so that you're creating a namespace for it rather than adding it as a function to the global scope 
