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
module exports = function(p1,p2,...) {
	  ...;
	};
````