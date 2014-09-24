A few notes on CSS pre-processors, not from any book in specific. I'll eventually turn these into a few beginners blog posts on the subject!

#Sass
* Sass is a compiler
  * A compiler is something that takes an input and turns it into an output
* Sass' output is CSS, as browsers _do_ understand CSS but not Sass
> Sass is for the developer and CSS is for the browser
* Sass is completely compatible with CSS so you can type pure CSS into an scss file and it wil work fine
* `gem install sass` to install
* Use `sass --watch .` in the terminal for it to compile your scss file into css as you code

###Nesting
> DRY - Don't Repeat Yourself

**Nesting** is the problem Sass was originally built to overcome.
Rather than having:
```css
.blog-entry h1 {<<css>>};
.blog-entry p {<<css>>};
.blog-entry a {<<css>>};
```
With Sass you can have
```css
.blog-entry{
  h1 {<<css>>};
  p {<<css>>};
  a {<<css>>};
}
```
This makes your code more maintainable down the line. **Don't go more than 4 nesting levels deep in your markup** (otherwise it's an indication you should probably have more IDs or classes).

###& symbol
The ampersand `&` character references the parent element, by essentially inserting it in the place of the `&`

```css
a {
  <<css>>;
  &:hover {<<css>>};
  &:visited {<<css>>};
}
/*becomes:*/
a {<<css>>};
a:hover {<<css>>};
a:visited {{<<css>>};}
```
Using the `&` character in a different way, the `div.footer & {color:purple;}` in the Sass below becomes `div.footer p a {color:yellow;}`

```css
p {
  a{
    color:red;
    div.footer & {color:yellow;}
  }
  >a{
    color:blue;
    &:hover{opacity:0.5;}
  }
}
```
###Variables
Setting a variable is as simple setting it at the top of your scss file with a `$` to denote a variable, for example `$primary-color: #fff`
* To use the variable, just replace place it into your code as you would a normal value: `h1 {color: $primary-color;}`
* You can manipulate variable mathematically if they're numbers, for example:
```css
$default-margin: 2em;

h1 { margin: $default-margin * 2;}
```
* When using mathematics, you can't mix

###Mixins
Mixins allow you to re-use snippets of code, but more than that, they are great for complex CSS situations and when you need to pass in arguments (see section on [@extend directive](#sass-extend) for situations of simple code re-use).
_If you find yourself using a code snippet 2 or more times, it's a good candidate for a mixin. If you find yourself using it **6 or more times**, it's a good candidate for a **global mixin**._
```css
/*from teamtreehouse.com
Create a mixin at the top of your scss file with the code you want to use in multiple places in your CSS*/
@mixin roundy {
  border-radius: 4px;
  border-top-right-radius: 8px;
  border-bottom-left-radius: 8px;
}
/*Then use it in your file wherever you like*/
.box {
  @include roundy; }
```
You can also pass in arguments to mixins:
```css
/*from teamtreehouse.com*/
@mixin roundy ($radius, $color) {
  border-radius: $radius;
  border-top-right-radius: $radius * 2;
  border-bottom-left-radius: $radius * 2;
  a {
    color: $color;
  }
}
/*Passing in 1em as the $radius value and blue as the color for links*/
.box {
  @include roundy(1em, blue); }
```
Mixins are fantastic for **passing in arguments** and for **complex logic** CSS.

<a name="sass-extend"/>
###@extend directive
If you do just want to do something simple like re-use a snippet of code, you can also use `@extend`.
* You can @extend an existing class, for example `.menu{ @extend .full-width-bar;}`
* Or you can create a selector which won't show up in your code, it'll just be a placeholder for some CSS, for example:
```css
%full-width-bar {
  height: 4em;
  width:100%;
}

.menu {
  @extend %full-width-bar;
  color: #fff;
}
```








#Asides
##Use Sass with Modernizr
[**Modernizr**](http://modernizr.com/) is a Javascript library that detects which CSS3 and HTML5 features are available for the browser in use.
* If you add Mordernizr to your HTML file, when your file is run in the browser, it will detect the features available and add a bunch of classes (such as `canvas`, `flexbox`, `sessionstorage` and many more) to your `<html>` tag
* Sass then also lets you use the `&` to essentially put in conditional CSS using these Modernizr classes, so for example in the code snippet below we're using the class `csscolumns` which may be added by Modernizr to browsers that support it, to add styling to `.blog-entry p` if the class `.csscolumns` exists:
```css
/*scss file:*/
.blog-entry p {
  color: blue;
  margin: 2em;
  html.csscolumns & {
      column-count: 2;
      margin: 1em;
  }
};
/*which will be output in CSS as:*/
.blog-entry p {
  color: blue;
  margin: 2em;
}

html.csscolumns .blog-entry p {
    column-count: 2;
    margin: 1em;
}
```
The beauty of this is that it **keeps all the styling of `.blog-entry p` in one place** in your scss file

#Research
Sass vs libsass (can be ported to other programming languages other than ruby?)
