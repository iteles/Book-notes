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

Some examples of filters:
* ``<li ng-repeat="product in products | limitTo:3">`` limits the number of products displayed to 3
* ``| orderBy: '-price'`` orders your array by descending price


* 2 way data binding means that that expressions such as {{tab}} are re-evaluated when a property changes
* ``ng-init`` allows you to initialise an expression to a certain value, so ``ng-init="tab = 1"`` initialised the ``tab`` expression to 1 within the scope of the element you add ``ng-init`` to
  * Initialisation should always go _inside a controller_ and ``ng-init`` should **only be used for prototyping** - in this case your controller would include ``this.tab = 1;``

To use `ng-class`:
* Format: `ng-class=" classYouWantToAdd: conditionToEvaluate"`
* Example: `<li ng-class="active:tab === 1">` where if the tab is tab 1, class 'active' will be added to the <li> element
  * This all gets a little bit like too much code in your HTML so you're actually set your active: a function expression in your controller that sets the tab `<li ng-class="active: tabIsSetTo(1)">`

###Forms:
* Data binding in forms:
  * `<input ng-model="review.terms" type="checkbox">` sets review.terms equal to true if the checkbox is checked
  *  `<input ng-model="review.color type="radio" value="green">` sets the value of review.color to green if this radio button is selected
* `ng-submit` allows you to call a function when submitting a form
* Validation
  * Switch off default HTML fom validation by adding `novalidate` to your form tag
  * Add `required` to required input elements
  * Angular has a built-in `$valid` property that you can call on forms to validate them
  * If you only want the form to submit only when it is valid, add the validation of the form as a condition in `ng-submit`, for example `ng-submit="<formName>.$valid && <functioncall>` (without the `<>` signs)
  * Angular add classes to the elements in the form as you're typing depending on whether the contents of the element are valid or not, which means you can add border-color styling to the elements to indicate valid or invalid fields
    * `ng-dirty` for when someone starts typing in a field (it's `ng-pristine` if the field is blank)
    * `ng-valid` is added when the contents are valid and `ng-invalid` for when they are not

**Using ng-include**
After the browser has fetched index.html from the server, it loads up Angular, finds `ng-include`, uses an Ajax request to fetch whatever is in ng-include from the server and loads it into the page
* You might use it to include an HTML snippet you use regularly in your code, but really it's best to use a custom directive for that

###Custom Directives:
> Directives allow you to write HTML that expresses the behaviour of your application

A custom directive might show up as `<productreview></productreview>` in your HTML - without ever having to interract with the app itself, you have a good idea of what this part of it includes - _expressiveness is the real power of custom directives_
* Note: some browsers don't like self-closing tags when using custom elements like this one, so don't use them with custom directives!

**Very basic directives: template-expanding directives**
* Directives return a directive definition object - a configuration object which defines how your directive will work
* Use element directives for UI widgets `<productreview></productreview>` and _attribute directives_ for mixins which add behaviours to your HTML (like a tooltip) `<h3 productreview></h3>`
  * To use an attribute directive, you'd specify `restrict: 'A'` in your directive (in your js file) - for elements you specify `restrict: 'E'`
  ```javascript
    //note that you intend to just use the element <product-description> in your HTML, but when creating your custom directive, the '-' is replaced with camel case
  app.directive("productDescription", function(){
    return{ //remember the directive returns the configuration object which determines how the directive will work
      restrict: 'E', //specifies the type of directive (element directive)
      templateUrl: 'product-description.html'
    };
  });
  ```

**Directive controllers:**
See learning folder on github for examples

###Refactoring into a Module
When you find you have too many controllers in one module (lots of people advocate having only one controller per module, but that depends on the application size and functionality), refactor it into various modules. Steps:
1. Create a new js file and a new module inside it
```javascript
(function() {
var app = angular.module('store-checkout', []);
} // because this var is in its own closure rather than in the global namespace it can also be named 'app'
```
2. Move your controller(s)/directive(s) into your new js file
3. Add your new module as a dependency in your previous module if that is the case: `var app = angular.module('main-store', ['store-checkout']);`
4. Don't forget to link the new js file in your HTML document
