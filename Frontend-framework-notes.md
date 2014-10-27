* Frameworks help solve typography, layout and browser compatibility issues
  * A complete FE framework might consist of UI components, JS plugins and styled typography

* Frameworks are for designers and developers who already know HTML, CSS and some JS but want a more organised approach

* Useful in a team because you're working off a standard codebase so everyone uses a similar coding style which can easily be picked up by another team
  * Makes development and maintenance easier and quicker
* Should not be used as a crutch
* Choose frameworks with really good documentation to lessen your learning curve
* Different frameworks are good for different projects:
  * If all you want to do is lay out a page, you might be better off with just a simple grid framework

##Bootstrap
* Can use the CDN (content delivery network) but have to be connected to the internet to use it
* Works best in latest browsers (so IE8 and older browsers it will still function but look slightly different)
* Built with LESS pre-processor Variables, mixins, nesting and other LESS elements
  * ZZZ - look for stuff on SASS
* Minified - all unnecessary comments, spaces and characters are stripped out so file sizes are smaller and can be downloaded much faster
* **Helper classes** are to help layout of page - like .pull-left and .pull-right
* Grid layouts are mobile first so adds @media queries to accommodate larger screen sizes
* Adds `<li class="divider"></li>` to put a divider in a dropdown menu or `<b class="carret"></b>` to add a little arrow next to a menu item

* Customizing Bootstrap:
  * If you want transparent colours, use rgba(x,x,x,x) where the last x is the alpha
  * rgba (0,0,0,0) give you a transparent background (useful for button for example)


* AAA - Look at button classes, notice how they're not names 'button red' or 'button green'

Foundation
* Uses `zepto.js` JS library by default which is a much lighter alternative to jQuery which works in every modern browser **except IE** - if you remove zepto.js file, it falls back to jQuery
