
*It should be noted that these notes contain the information I felt was most relevant from the book and are by no means exhaustive. They are a quick reference and I highly recommend going through the book independently.*

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

<a name="Chapter7"/>
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
###Opening, reading and writing to files

* To read or write to a file, you must first open is with `fs.open`, passing in a file path as the first argument and a flag as the second argument that indicates whether you want to read (r), read and write (r+), write a new file - or truncates an old one to zero length (w), read and write to a new file (w+), write text to the end of an existing file (a) or open an existing file for reading and appending text (a+)

* You must close files you open - you do this by keeping track of the open file descriptors and closing them using `fs.close(fd, callback)`

* It is, however, *much safer to use _ReadStream_s and _WriteStream_s* than fs.open, fs.write and fs.close processes as these are *safe to use concurrently where you may have two or more pending read or write processes* (more information in [Chapter 9](#Chapter9))


<a name="Chapter9"/>
##Chapter 9 - Reading & Writing Streams of Data

###Readable Streams
* Streams send data in chunks and as *buffers* by default - they're like "data faucets"
* Listen for the 'data' event
```javascript
readable stream1.on('data', function(data){
	console.log('Got this data: data');
}); //every time a chunk of data comes through it will be printed to the console log
```
* If you know your data is a UTF-8 encoded string, have it in your code `stream1.setEncoding('utf8')` so that the stream knows how to deliver your characters
* Pause the data stream with `stream.pause();` and resume it with `stream.resume();`
* To know *when the stream ends, listen for the 'end' event*

###Writable streams

A writable stream is an object you can write data into.

* The 'write' command returns _true_ if the buffer is immediately flushed or _false_ if it was queued instead `writable_stream.write('string of text')`

_In practice_ you would use read or write streams with a file, pass it a file path as the first argument and a number of optional arguments enclosed in curly brackets {} as a second argument. You can also use an existing file, see page 79 for code examples.
```javascript
var fs = require('fs');
var rs = fs.createReadStream('./path/to/file', {optional second arguments go here});
...
```
* Once _all_ buffers are flushed, the stream emits a 'drain' event that you can listen to

When a readStream is reading data faster that it can be written to the writeStream, this creates the *slow client problem* because you have to buffer the data and it gets backed up
* To avoid this problem, you can use `stream.pause()` and `stream.resume()` to control the flow of the readStream, _however:_
* This is so common that is has been captured in Node, where you can use `readStream.pipe(writeStream)`
	* `'pipe` also automatically ends the stream when it is completed although you can prevent this by using the second _options_ argument `readStream.pipe(writeStream, {end: false})` if desired

<a name="Chapter10"/>
##Chapter 10 - Building TCP Servers

When a new connection is established with the server, you are also handed a `socket` object as the first argument of the callback function.

The *socket* object is both a *read and write stream*, which means it:
	* emits 'data' events when it receives data (so you can listen for them and act on this)
	* emits the 'end' event when the connection is closed AND
	* you can write buffers or strings to it using `socket.write()`
```javascript
//require 'net'because we're building a TCP server in this chapter, not 'http' for example

var server = require('net').createServer(function(socket){
	console.log('A new connection has been created');

	socket.setEnconding('utf8');
	socket.write("Hello! Welcome to the command line.");
	socket.end();
	console.log('Client connection has now ended');
	server.close(); //closes server
}).listen(4001); //remember that the first line where var server is created doesn't actually end until here!
//to try this out, launch the server from the command line & then establish a new connection to it (typing 'telnet localhost 4001' also into the command line)
```
* Rather than using `socket.write()`, we can also use `socket.pipe()` to pipe the contents of that socket directly into a text file for example (remember to create the fs writeStream at the top of your code!)
	* The text file doesn't need to already exist, it will be created when you start piping in content
	* You would start your server with node and then in another terminal window, telnet into the port and then whatever you write in _that_ terminal window would be what would be piped into the text file

* You can set the connection to timeOut after a certain amount of time or to close when no traffic is being received
* You can also implement a keep-alive mechanism (`socket.setKeepAlive(true,<optionalTimeFrame>)`) which will periodically send empty packets to keep the connection open on both peers (seems a little bit wasteful though?)
* If you find there is some latency in your application, you can force data to be sent immediately with `socket.setNoDelay(true)`

Errors should be handled using the following pattern, or Node will terminate the current process:
```javascript
require('net').createServer (function(socket){
	socket.on('error', function(error){
		//do something
	});
});
```
In the book there is a great step-by-step example of building a simple TCP server. The final, commented code, can be found in my [Professional-Node-js-exercises folder in my _learning_ github repository](https://github.com/iteles/learning/)

<a name="Chapter11"/>
##Chapter 11 - Building HTTP Servers
"One of the preferred application deployment mechanisms is to provide an HTTP service on the internet that answers HTTP client requests." - uses TCP as its transfer protocol.
```javascript
//very basic Hello World example
require('http').createServer(function(request, response) {
	response.writeHead(200, {'Content-Type': 'text/plain'});
	response.write('Hello World!')
	response.end('It all ends here'); //accepts a string that it writes to the response object before ending the request
}).listen(4000); //binding to port 4000
```
* When a client makes a request, the HTTP server emits a `request` event, which gives you access to the properties of the request.
* An HTTP `response` object is also passed in, which allows you to build up an HTTP response which will be sent back to the client

####Understanding the HTTPS.ServerRequest object
You can access the following request (req) object properties:
* `req.url`: Contains requested URL as a string, e.g in example above, http://localhost:4000/ would write out '/' but http://localhost:4000/abcde would write out 'abcde'
* `req.method`: HTTP method the request used, e.g. GET, PUT, POST, DELETE or HEAD
* `req.headers`: Contains a property for each HTTP header on the request
	* To inspect the headers on a request however, you would use it something like this (doesn't have to be with req.end()): `req.end(util.inspect(req.headers));`

Interestingly, the request object is a _readStream_ and emits a 'data event' so if you wanted, you could pipe it into a file as it arrived:
```javascript
req.on('data', function(data) {
	data.write(writeStream);
});
```
####Understanding the HTTPS.ServerResponse object
This response (res) object is used to reply to the client. You can use:
* `res.writeHead(status, headers)` to write the header for the response, where status 200 means 'success' for example
* `res.setHeader(name, value)` to change the header, but only works if you haven't already sent any of the body of the response
* `res.write(contents)` to write text or a buffer to the body of the response

You can pipe any readStream into the response. If you wanted to pipe in a movie for example, you would create the readStream with `var rs = fs.createReadStream('test.mp4')` and then pipe it in using `rs.pipe(res);`.
If you then open your browser to the port you bound the server to, the movie should start playing straight away even though it hasn't fully loaded because Node uses _HTTP chunked encoding_

You can also pipe in another process (such as a child process for example).

<a name="Chapter12"/>
##Chapter 12 - Building a TCP Client
_Skimmed through this chapter as not immediately relevant to what I'm doing, may come back to it for detailed noted_
* TCP is one of the most used transport protocols in the world
* HTTP which is an application protocol, sits atop it
* TCP guarantees that the messages you receive are in order
	* Note: When you write a TCP stream, you receive no acknowledgement that your data has been sent
* After you have ended you connection using `conn.end()`, the connection does not end immediately as you are queueing the operation - this means you can still receive some packets of data

####Using the command line
* When a Node process starts, `process.stdin` is a readStream created by this process to accept keyboard input, initialised in the paused state
 * You must resume it first using `process.stdin.resume();` and it will then start emitting data events when the user starts typing
 * Once resumed, you can pipe this readStream of user input into conn, the writable stream to the server `process.stdin.pipe(conn)`

<a name="Chapter13"/>
##Chapter 13 - Making HTTP Requests
> HTTP has become the preferred way of serving and consuming public-facing API calls.



[Interesting link on what Node.js is and why you would use it](http://www.toptal.com/nodejs/why-the-hell-would-i-use-node-js)
