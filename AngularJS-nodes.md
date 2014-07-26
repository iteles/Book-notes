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


#####Model-View-Controller (MVC)
The key idea with the MVC application structure is a separation of data (model), the application logic (controller) and what the user sees on their screen (view).
