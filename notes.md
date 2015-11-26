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
File Modules#
If the exact filename is not found, then Node.js will attempt to load the required filename 
with the added extensions: .js, .json, and finally .node.

.js files are interpreted as JavaScript text files, and .json files are parsed as JSON text files. .node files are 
interpreted as compiled addon modules loaded with dlopen.

A required module prefixed with '/' is an absolute path to the file. For example, require('/home/marco/foo.js') 
will load the file at /home/marco/foo.js.

A required module prefixed with './' is relative to the file calling require(). That is, circle.js must be in the 
same directory as foo.js for require('./circle') to find it.

Without a leading '/', './', or '../' to indicate a file, the module must either be a core module or is loaded from a node_modules folder.

If the given path does not exist, require() will throw an Error with its code property set to 'MODULE_NOT_FOUN
It is convenient to organize programs and libraries into self-contained directories, and then provide a single entry point 
to that library. There are three ways in which a folder may be passed to require() as an argument.

The first is to create a package.json file in the root of the folder, which specifies a main module. 
An example package.json file might look like this:

{ "name" : "some-library",
  "main" : "./lib/some-library.js" }
If this was in a folder at ./some-library, then require('./some-library') would attempt 
to load ./some-library/lib/some-library.js.

This is the extent of Node.js's awareness of package.json files.

If there is no package.json file present in the directory, then Node.js will attempt to load an index.js or index.node 
file out of that directory. For example, if there was no package.json file in the above example, then require('./some-library') would attempt to load:

./some-library/index.js
./some-library/index.node
Loading from node_modules Folders#
If the module identifier passed to require() is not a native module, and does not begin with '/', '../', or './', 
then Node.js starts at the parent directory of the current module, and adds /node_modules, and attempts to load the module from that location.

If it is not found there, then it moves to the parent directory, and so on, until the root of the file system is reached.

For example, if the file at '/home/ry/projects/foo.js' called require('bar.js'), then Node.js would look in the following locations, 
in this order:

/home/ry/projects/node_modules/bar.js
/home/ry/node_modules/bar.js
/home/node_modules/bar.js
/node_modules/bar.js
This allows programs to localize their dependencies, so that they do not clash.

You can require specific files or sub modules distributed with a module by including a path suffix after the module name. 
For instance require('example-module/path/to/file') would resolve path/to/file relative to where example-module is located. 
The suffixed path follows the same module resolution semantics.

Loading from the global folders#
If the NODE_PATH environment variable is set to a colon-delimited list of absolute paths, then Node.js will search those paths for modules 
if they are not found elsewhere. (Note: On Windows, NODE_PATH is delimited by semicolons instead of colons.)

NODE_PATH was originally created to support loading modules from varying paths before the current module resolution algorithm was frozen.

NODE_PATH is still supported, but is less necessary now that the Node.js ecosystem has settled on a convention for locating dependent modules. 
Sometimes deployments that rely on NODE_PATH show surprising behavior when people are unaware that NODE_PATH must be set. Sometimes a module's 
dependencies change, causing a different version (or even a different module) to be loaded as the NODE_PATH is searched.

Additionally, Node.js will search in the following locations:

1: $HOME/.node_modules
2: $HOME/.node_libraries
3: $PREFIX/lib/node
Where $HOME is the user's home directory, and $PREFIX is Node.js's configured node_prefix.

These are mostly for historic reasons. You are highly encouraged to place your dependencies locally in node_modules folders. 
They will be loaded faster, and more reliably.

The module Object#
{Object}
In each module, the module free variable is a reference to the object representing the current module. For convenience, module.exports 
is also accessible via the exports module-global. module isn't actually a global but rather local to each module.

module.children#

Array
The module objects required by this one.

module.exports#

Object
The module.exports object is created by the Module system. Sometimes this is not acceptable; many want their module to be an 
instance of some class. To do this, assign the desired export object to module.exports. Note that assigning the desired object 
to exports will simply rebind the local exports variable, which is probably not what you want to do.

For example suppose we were making a module called a.js

var EventEmitter = require('events');

module.exports = new EventEmitter();

// Do some work, and after some time emit
// the 'ready' event from the module itself.
setTimeout(function() {
  module.exports.emit('ready');
}, 1000);
Then in another file we could do

var a = require('./a');
a.on('ready', function() {
  console.log('module a is ready');
});
Note that assignment to module.exports must be done immediately. It cannot be done in any callbacks. This does not work:

x.js:

setTimeout(function() {
  module.exports = { a: "hello" };
}, 0);
y.js:

var x = require('./x');
console.log(x.a);
exports alias#

The exports variable that is available within a module starts as a reference to module.exports. As with any variable, 
if you assign a new value to it, it is no longer bound to the previous value.

To illustrate the behavior, imagine this hypothetical implementation of require():

function require(...) {
  // ...
  function (module, exports) {
    // Your module code here
    exports = some_func;        // re-assigns exports, exports is no longer
                                // a shortcut, and nothing is exported.
    module.exports = some_func; // makes your module export 0
  } (module, module.exports);
  return module;
}
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