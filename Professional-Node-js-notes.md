##Chapter 1 - Installing Node

_Node Package Manager_ (NPM) allows you to download, install and manage third party modules. Node only runs low-level APIs so if you don't want to have to (re)do all the work yourself, NPM is almost always necessary.


NPM can install packages:
	* **Global mode** which should only be used for modules that must always be available globally
		* To install a module use `$ npm install -g <package name>` or `$ sudo npm install -g <package name>`
		* In you need this module in any Node script you can write `var packagename = require ('<package name>')`
		* Any executable files are stored under `/usr/local/bin`
	* **Local mode** is the default mode and NPM installs the package in the a `/node_modules` folder in the current directory
		* A local module will **always take precedence** over a global module
		* Always use local mode if in doubt
		* Any executable files are stored under `/node_modules/.bin`

From the command line:
* `$ npm install <package name>` installs latest version of package **and the packages it depends on**
* `$ npm install <package name>@<version number>` installs specific version of package
	* `<version number>` can also refer to a range - use double quotes to envelope the range e.g. `"<0.3"`
* `$ npm uninstall <package name>` uninstalls the package (can also be used with the -g flag for global mode)
* `$ npm update <package name>` updates the package to the latest version (can also be used with the -g flag for global mode)

You can add your application's dependencies to a `package.json` file (p.12 for example) and when you type `$ npm install` whist in the application root directory, it will analyse this file and install the dependencies - similarly, use `$ npm update` to update all the dependencies.

##Chapter 3 - Loading Modules

Node implements the CommonJS standard which essentially solves JavaScript's global namespace issue by ensuring each module has its own context and doesn't interfere with other modules. **There is no such thing as the global namespace in Node.**

Loading a module via `var module = require('modulename')` returns a JavaScript object representing the JavaScript API exposed by the module, for example functions, objects or arrays.

Complex applications should be divided into reusable modules:
```javascript
function Circle(x, y, z){
	//functions here
}
module.exports = Circle; //determining what will be exported to other scripts
//Here we export the Circle constructor function, but more than one thing can be exported from a single module
```
Loading various module types:
	* **Core module** - refered to solely by name, not by file path
	* **File module** - non-core module which can be loaded by providing the absolute path in the `require` statement
	* **Folder module** - providing a path for a folder in the `require` statement; this folder _must_ include a `package.json` file defining what should be loaded
	* If you use modulename.js in the `require` statement without a path, Node will look for the file in the /node_modules folder of the current directory; if it doesn't find the module, it will keep searching parent modules until it reaches the root folder

Modules are **initialized only once** and cached the first time, no matter how many times they are loaded with the `require` statement

##Chapter 7 - Querying, Reading from and Writing to Files

###Manipulating file paths

Remember to require the path module using `var path = require('path');`

* `path.normalize('<filepath>')` normalizes the file path, useful before storing (worth printing out the result to make sure this is as expected)
* `path.join('<filepath1>, <filepath2>')` joins the paths together and _normalizes the path_
* `path.relative('<filepath1>, <filepath2>')` find the relative path between the two file paths (e.g. '../../dir/blb' would mean the two files have the same first and second level directories but differ after that)
* `path.dirname('<filepath1>')` provides path to directory of your file
* `path.basename('<filepath1>')` provides the name of the file, including extension
	* If you pass it a second argument containing the file extension (e.g. '.html'), it will remove it from the result
* `path.extname('<filepath1>')` provides the file extension, including the '.'

To find out if a path exists, you must use `fs.exists` (and require the fs module). This function does I/O so you have to pass it a callback:
```node
var fs = require('fs');
fs.exists('<filepath>', function(exists) {
	console.log('exists': exists);
	//=> true/false
});
```




[Interesting link on what Node.js is and why you would use it](http://www.toptal.com/nodejs/why-the-hell-would-i-use-node-js)