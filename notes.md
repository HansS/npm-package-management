##Npm Package Management
###A Node Module
To make a function available for a node developer it must be made
available through a node module. A node module can be a single file
or a directory with a file index.js in it which in turn can expose
functions and objects.

Node has adopted the CommonJs Standard for creating modules.
To make Javascript functionality available to a node developer the author of a 
javascript module has to explicitly export the desired functionality with the keyword
````node
module.exports = function(p1,p2,...) {
	  ...;
	};
````
###Node Modules
Node.js has a simple module loading system. Files and modules are in a 1-1 relationship. 
As an example, foo.js loads the module circle.js in the same directory.

The contents of foo.js:
```node
var circle = require('./circle.js');
console.log( 'The area of a circle of radius 4 is '
           + circle.area(4));
The contents of circle.js:

var PI = Math.PI;

exports.area = function (r) {
  return PI * r * r;
};

exports.circumference = function (r) {
  return 2 * PI * r;
};
```
The module circle.js has exported the functions area() and circumference(). 
To add functions and objects to the root of your module, you can add them to the special exports object.

Variables local to the module will be private, as though the module was wrapped in a function. 
In this example the variable PI is private to circle.js.

If you want the root of your module's export to be a function (such as a constructor) or if you want to export 
a complete object in one assignment instead of building it one property at a time, assign it to module.exports instead of exports.

Below, bar.js makes use of the square module, which exports a constructor:
```node
var square = require('./square.js');
var mySquare = square(2);
console.log('The area of my square is ' + mySquare.area());
The square module is defined in square.js:

// assigning to exports will not modify module, must use module.exports
module.exports = function(width) {
  return {
    area: function() {
      return width * width;
    }
  };
}
```
###Node Core Modules
Module | Description
----|-----
fs | I/O Filesystem


###Resolving Node Modules
Node Module Type | how to load module | file location
--------|---------------------
core module | var m = require('m'); | root/node_modules
user module | var um = require('./um') | root
###Loading User Node Modules either
* with package.json
* with index.js // javascript code
* with index.node // c code