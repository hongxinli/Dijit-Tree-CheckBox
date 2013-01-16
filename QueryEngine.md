# Query Engine

The cbtree **/cbtree/store/util/QueryEngine** provides the search and query
functionality for all cbtree stores. The name "query engine" is somewhat
misleading in the sense that it does NOT actually query the store, instead it
returns a function capable of executing the required query.  

The Query Engine is **_NOT_** an implementation of the **cbtree/store/api/Store API**
query() function, it provides the functionality to enable the implementation of
such a function. The Query Engine can be used with any arbitrary set of JavaScript
key:value pairs objects, not just store objects.

<h2 id="wiki-queryEngine"></h2>
#### queryEngine( query, queryDirectives? )
> Returns a JavaScript function capable of executing a store query.

**_query:_** Object | Function | String
> The query argument is either a JavaScript key:value pairs object, a function or
> a string. Please see [The Query Argument](#wiki-the-query-argument) below for
> a detailed description an examples.


**_queryDirectives:_** [queryDirectives](Store-API#wiki-queryDirectives)?
> The queryDirectives parameter is a JavaScript key:value pair object providing
> additional sort and pagination details to be applied to the query result.

**returns:** Function
> A JavaScript function referred to as "The Query Function". 
> (See [The Query Function](#wiki-the-query-function) below).

<h2 id="wiki-the-query-argument">The Query Argument</h2>
As stated above, the query argument passed to the Query Engine can be a JavaScript
object, a function or a string. The next sections provide a detailed description
of their usage and some examples. 
All examples use the same [dataset](#wiki-sample-data) listed at the bottom.

### Object as Query Argument
If the query argument is a key:value pairs object, each	key:value pair is matched
with the corresponding key:value pair of	the store objects unless the query
property value is a function in which case the function is called as: 
**func(value, key, object)**.
Query property values can be a string, a number, a regular expression, an object
providing a test() method, an	array of any of the previous types or a function.  

The following is the ABNF notation of the query object:

	query-object   ::= "{" query-property ["," query-property]* "}";
	query-property ::= property-name ":" property-value
	property-name  ::= js-identifier
	property-value ::= value-array | value | Function
	value-array    ::= "[" value ["," value]* "]"
	value          ::= String | Number | RegEx

Next, some examples of the query argument as an object are:

	{ name: "Homer" }
	{ name: /^Homer$/ }
	{ parent: ["Homer", "Marge"], checked: true }
	{ parent: /^(Homer|Marge)$/ }
	{ name: function (value, key, object) {
	          if (/^M/.test(value) && object.age > 20) {
	            return true;
	          }
	        }
	}

### Function as Query Argument
If the query argument is a function, the fuction is called once for every store
object with the store object as its argument: **queryFilter(object)**.
The function must return boolean true or false. For example:

	require(["cbtree/Memory"], function (Memory) {
	  function myFilterFunc( storeObject ) {
	              ...
	    return true;
	  }

	  var myStore = new Memory( { ... });
	              ...
	  var result = store.query( myFilterFunc, {count:5, ...});
	}

### String as Query Argument
If the query argument is a string, the string value is the name of a store method.
This options is not used with the default cbtree stores unless you extend the
store with your own functionality. An example of such store extension could
look like:

	require(["cbtree/Memory"], function (Memory) {
	  function myFilterFunc( storeObject ) {
	              ...
	    return true;
	  }

	  var myStore = new Memory( {myQFilter: myFilterFunc, ... });
	              ...
	  var result = store.query( "myQFilter", {count:5, ...});
	}


Alternatively you could use the **dojo/_base/lang** extend() method to extend
the store or pass the function <em>myFilterFunc</em> directly as the query argument.  
For additional information see the <em>Query as a Function</em> section above.

<h2 id="wiki-the-query-function">The Query Function</h2>
The Query Engine, when called, returns a function that takes an array of objects
as its only argument, this function is referred to as "The Query Function".
Typically, in the context of a store, the Query Function is passed an array with
all store objects and on completion returns a new array with all store objects
that matched the query.  

	require(["./queryEngine", 
	              ...
	        ], function ( queryEngine, ... ) {

	  var store = declare([], {
	    queryEngine: queryEngine,
	    storeObjects: [],

	              ...
	
	    query: function ( query, queryDirectives ) {
	      var queryFunc = this.queryEngine( query, queryDirectives );
	      var matches   = queryFunc( this.storeObjects );
	    
	      return matches;
	    }

	  }); /* end declare() */

	  return store;
	  
	}); /* end require() */
	
The signature of the Query Function returned by the Query  Engine is as follows:

#### queryFunction( objects[] )
> Executes the query on an array of JavaScript objects.

**_objects:_** Object[]
> An array of JavaScript key:value pairs objects.

**returns:** Object[]
> An array of JavaScript key:value pairs objects that matched the query.

### Alternative Usage

As stated before, the usage of the Query Engine is not limited to just object
stores. It can be used with an arbitrary array of JavaScript key:value pairs
object as the following example demonstrates:

	require(["./queryEngine", 
	              ...
	        ], function ( queryEngine, ... ) {

	  var queryFunc = queryEngine( {innerHTML:/Tutorial/} );
	  var anchors   = Array.prototype.slice.call( document.anchors );

	  var result = queryFunc( anchors );
	});

The example above gets all the anchors in a document and queries them looking
for any that have the word "Tutorial" in the innerHTML property.



<h2 id="wiki-using-regular-expressions">Using Regular Expressions</h2>
Regular Expressions (RegEx) can be used to query a store object property whenever 
the property value has a _test()_ method.

<h2 id="wiki-using-query-options">Using Query Options</h2>

<h2 id="wiki-querying-arrays">Querying Arrays</h2>
The cbtree Query Engine provide full support for querying store object properties
whose value is an array. In addition, as shown in the ABNF notation, query 
object properties can also be specified as an array of values or regular 
expression of a combination of those. This section details how the cbtree
Query Engine deals with arrays and array values. To better explain the Query
Engine behavour lets assume we have two store objects defined as follows:

	{ name:"Homer", age: 42, parents:["Abe", "Mona"], hair: "none",  children:["Bart", "Lisa", "Maggie"] }
	{ name:"Ned"  , age: 40, parents:["Root"],        hair: "brown", children:["Rod", "Tod"] }

The store object properties _name_, _age_ and _hair_ are all single value properties
whereas _parents_ and _children_ are both array value properties.

1. If the store object property is a **single** value property and the query object 
	property value is an array, a logical OR operation is performed.  
	For example, the next query returns both store objects:

	store.query( { name:["Homer","Ned"] } )

2. If the store object property is an **array** value property and the query 
	object property value is specified as a **single** value, then only those store
	objects are returned whose **array** value property contain the query object
	property value. For example, the following query only returns the first store
	object
	
	store.query( { children:"Bart" } );

3. If both the store object property and query object property are arrays, a
	logical AND operations is performed. That is, only those store objects are
	returned whose **array** value property contains **ALL** values in the query
	object value array. For example, the next query only returns the first store
	object:
	
	store.query( { children:["Bart", "Lisa"] } );
	
	Whereas the next query returns no objects:
	
	store.query( { children:["Bart", "Lisa", "Ned"] } );

4. If the store object property is an **array** value property and the query 
	object property value is a regular expression than any store objects whose
	**array** value property contain a value that matches the regular expression
	is returned. For example, the following query returns both store objects:

	store.query( { children:/(L|R)/ } );
	

<h2 id="wiki-querying-a-store">Querying a Store</h2>

<h2 id="wiki-sample-data">Sample Data</h2>
All examples listed on this page use the same dataset as shown below. The data is a JSON
encoded file called 'Simpsons.json': 

	[
		{ "name":"Root"  ,"parent":[] },

		{ "name":"Lisa"  ,"age":10, "parent":["Homer","Marge"], "hair":"blond", "checked":true },
		{ "name":"Bart"  ,"age":9, "parent":["Homer","Marge"], "hair":"blond", "checked":false },
		{ "name":"Maggie","age":2, "parent":["Homer","Marge"], "hair":"black", "checked":true },

		{ "name":"Patty","age":37, "parent":["Jacqueline"], "hair":"blond", "checked":true },
		{ "name":"Selma","age":38, "parent":["Jacqueline"], "hair":"blond", "checked":false },

		{ "name":"Rod" ,"age":9, "parent":["Ned"], "hair":"blond", "checked":true },
		{ "name":"Todd","age":8, "parent":["Ned"], "hair":"blond", "checked":true },

		{ "name":"Abe"  ,"age":65, "parent":["Root"], "hair":"none", "checked":true },
		{ "name":"Mona" ,"age":65, "parent":["Root"], "hair":"none", "checked":false },
		{ "name":"Jacqueline","age":63, "parent":["Root"], "hair":"none", "checked":true },
		{ "name":"Homer","age":42, "parent":["Abe","Mona"], "hair":"none", "checked":false },
		{ "name":"Marge","age":35, "parent":["Jacqueline"], "hair":"blond", "checked":true },
		{ "name":"Ned"  ,"age":40, "parent":["Root"], "hair":"brown", "checked":true },

		{ "name":"Apu","age":41, "parent":["Root"], "hair":"black", "checked":true },
		{ "name":"Manjula","age":40, "parent":["Apu"], "hair":"brown", "checked":false}
	]

The file is loaded into the store using the following syntax:

	var store = new Hierarchy( {url:"./Simpsons.json"});


