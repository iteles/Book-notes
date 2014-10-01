> Controllers are where we define our app's behaviour by defining functions and values

Recommended: Wrap entire application in a closure - it's a good habit to get into
```javascript
//wrap app in closure
function(){
  //empty square brackets are where dependencies are added
  var app = angular.module('gemStore', []);
}
```

``<h1>{{product.price | currency}}</h1>`` pipes ('send the output into') the currency filter
``{{data | filter:options}}`` is the standard format

* 2 way data binding means that that expressions such as {{tab}} are re-evaluated when a property changes
* ``ng-init`` allows you to initialise an expression to a certain value, so ``ng-init="tab = 1"`` initialised the ``tab`` expression to 1 within the scope of the element you add ``ng-init`` to
  * Initialisation should always go _inside a controller_ and ``ng-init`` should **only be used for prototyping** - in this case your controller would include ``this.tab = 1;``
