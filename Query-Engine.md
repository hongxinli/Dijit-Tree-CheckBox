# Query Engine

The **cbtree/store/util/QueryEngine** provides the underlying search and
query functionality for all cbtree stores. The name "query engine" is somewhat
misleading in the sense that it does NOT actually query the store, instead when 
called, it returns a function capable of executing the required query.  

The Query Engine is **_NOT_** an implementation of the **cbtree/store/api/Store API**
query() function, it provides the functionality to enable the implementation of
such a function. The Query Engine can be used with any arbitrary set of JavaScript
key:value pairs objects, not just store objects.

<h2 id="wiki-queryEngine"></h2>
#### queryEngine( query, options? )
> Returns a JavaScript function capable of executing a store query and optionally 
> apply pagination and sorting to the result.

**_query:_** Object | Function | String
> The query argument is either a JavaScript key:value pairs object, a function or
> a string. Please see [The Query Argument](#wiki-the-query-argument) below for
> a detailed description an examples.


**_options:_** [queryDirectives](Store-API#wiki-queryDirectives)?
> The options parameter is a JavaScript key:value pair object providing
> additional sort and pagination details to be applied to the query result.
> See the [query options](#wiki-the-query-options) section below.

**returns:** Function
> A JavaScript function referred to as "The Query Function". 
> (See [The Query Function](#wiki-the-query-function) below).

<h2 id="wiki-the-query-argument">The Query Argument</h2>
The query argument passed to the Query Engine can be an object,
a function or a string. The next sections provide a detailed description
of their usage and some examples. 
All examples use the same [dataset](#wiki-sample-data) listed at the bottom.

### Object as Query Argument
If the query argument is a key:value pairs object, each	key:value pair is matched
with the corresponding key:value pair of the store objects unless the query
property value is a function or a regular expression. If the query property value
is a function the function is called as: **func(value, key, object)**.  
Query property values can be a string, a number, a regular expression, an	array
of any of the previous types or a function. If the query property value is an
array of strings you can also mixin regular expressions with the array. 

The following is the ABNF notation of the query object:

	query-object   = "{" query-property *("," query-property) "}";
	query-property = property-name ":" property-value
	property-name  = js-identifier
	property-value = value-array / value / Function
	value-array    = "[" value *("," value) "]"
	value          = String / Number / RegEx

Next, some examples of the query argument as an object are:

	{ name: "Homer" }
	{ name: /^Homer$/ }
	{ parent: ["Homer", "Marge"], checked: true }
	{ parent: /^(Homer|Marge)$/ }
	{ colors: ["blue", "red", /^gr/ }
	{ name: function (value, key, object) {
	          if (/^M/.test(value) && object.age > 20) {
	            return true;
	          }
	        }
	}

The last example above demonstrates the use of a function as the query property 
value. In this case an object is considered a match if its name starts with a
capital letter 'M' and the value of its *age* property is greater than 20.

### Function as Query Argument
If the query argument is a filter function, the filter function is called once
for every object in the  object array passed to _[The Query Function](#wiki-the-query-function)_.
The argument passed to the filter function is a single object as in: 
**filterFunc(object)**. The filter function must return Boolean true, indicating 
a match, or false if no match.  
For example:

	require(["cbtree/store/util/QueryEngine"], function ( QueryEngine ) {

	  function myFilterFunc( object ) {
	              ...
	    return true;
	  }

	  var queryFunc = QueryEngine( myFilterFunc, {count:5, ...} );
	  var results   = queryFunc( myObjects );
	              ...
	}

### String as Query Argument
If the query argument is a string, the string value is the name of a store function
that returns Boolean true or false. In order to use this approach with the cbtree
stores you must first extend the store with the required filter functionality.
An example of such store extension could look like:

	require(["cbtree/Memory"], function (Memory) {
	  function myFilterFunc( object ) {
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




<h2 id="wiki-the-query-options">The Query Options</h2>
The query options argument is an additional set of optional properties that are
passed to the Query Engine and are applied to the query results by the Query Function. 
In general, the query options effect the total number and range of the objects
returned and their order. Note: both pagination and sort operations are
applied to the result set, that is, after the query is executed.

#### Paginate Results
To return a subset of the query results the Query Engine support two optional
properties: **_count_** and **_start_**. The **_start_** property identifies the 
index (zero based) of the first object in the result set to be returned. The 
**_count_** property specifies the maximum number of objects to be returned.  
If no sort options are supplied the result set is returned in the natural order, 
if any, maintained by the store. (See the Natural and Hierarchy stores for details).

	var queryFunc = QueryEngine( {hair:"blond"}, {start:5, count:10});
	var results   = queryFunc( myObjects );

The above example returns at a maximum 10 objects whose *hair* property equal
"blond" starting at index number 5 of the results.

Please note, pagination is performed after any optional sort is performed.

#### Sorting Results
Query results can be sorted using the optional **_sort_** property. The **_sort_**
property value can be an array of, per property, sort directives or a compare
function.  
A [sort directive](wiki/Store-API#wiki-sortDirective) is a JavaScript key:value
pairs object describing what property to sort on, the direction of the sort and
if operation property values should be compared case insensitive.

	var sortSet   = [ {property:"name", descending:true, ignoreCase:true}, {property:"age"} ];
	var queryFunc = QueryEngine( {hair:"blond"}, {sort:sortSet, start:5, count:10});
	var results   = queryFunc( myObjects );


For additional query options information please refer to the 
[queryDirectives](wiki/Store-API#wiki-queryDirectives) section of the Store API.




<h2 id="wiki-the-query-function">The Query Function</h2>
The Query Engine, when called, returns a function that takes an array of objects
as its only argument, this function is referred to as "The Query Function".
Typically, in the context of a store, the Query Function is passed an array with
all store objects and on completion returns a new array with all store objects
that matched the query.

The signature of the Query Function returned by the Query  Engine is as follows:

#### queryFunction( objects[] )
> Executes the query on an array of JavaScript objects and optionally applies
> sorting and pagination to the result.

**_objects:_** Object[]
> An array of JavaScript key:value pairs objects.

**returns:** Object[]
> An array of JavaScript key:value pairs objects that matched the query.


The following example shows a basic implementation of the **cbtree/store/api/Store.query()**
function using the Query Engine and Query Function.

	require(["dojo/store/util/QueryResult",
	         "cbtree/store/util/QueryEngine", 
	              ...
	        ], function ( QueryResult, QueryEngine, ... ) {

	  var store = declare([], {
	    queryEngine: QueryEngine,
	    storeObjects: [],

	              ...
	
	    query: function ( query, queryDirectives ) {
	      var queryFunc = this.queryEngine( query, queryDirectives );
	      var matches   = queryFunc( this.storeObjects );
	    
	      return QueryResult( matches );
	    }

	  }); /* end declare() */

	  return store;
	  
	}); /* end require() */
	
### Other Usage

As stated before, the usage of the Query Engine is not limited to just object
stores. It can be used with an arbitrary array of JavaScript key:value pairs
object as the following example demonstrates:

	require(["cbtree/store/util/QueryEngine", 
	              ...
	        ], function ( queryEngine, ... ) {

	  var queryFunc = queryEngine( {innerHTML:/Tutorial/} );
	  var anchors   = Array.prototype.slice.call( document.anchors );

	  var result = queryFunc( anchors );
	              ...
	});

The example above gets all the anchors in a document and queries them looking
for any that have the word "Tutorial" in the innerHTML property.




<h2 id="wiki-matching-objects">Matching Objects</h2>
Objects are matched based on the value(s) of its properties. To test an object 
property value the _[Query Function](#wiki-the-query-function)_ offers three
basic methods:

1. The equal (==) operator.
2. Regular expressions.
3. Custom function.

Of course, as with anything else, each approach has its pros and cons.

#### Using 'is equal'

Using the 'is equal' method is the simplest and fastest approach, the Query Function
merely tests if two values are equal assuming both values are the same data type.
If you are looking to match objects based on an exact property value match, than 
this is the way to go. Example query arguments are:

	{ hair:"brown" }
	{ hair:"brown", checked:true }
	{ hair:"brown", checked:true, age:65 }


#### Using Regular Expressions
Regular Expressions (RegEx) can be used to query object properties whenever 
the property value has a _test()_ method like any string type does. Regular 
expression offer a huge flexibility in terms of matching values. However
only use regular expressions when your query can **NOT** be achieved using the
simpler 'is equal' approach. This because regular expression can be
expensive in terms of performance especially if your expression is not optimized
causing potentially hundreds of iterations on a single property value. Example 
query arguments using regular expressions are:

	{ name:/^H/ }
	{ name:/^(H|M)/
	{ filename:/^(.*)\.txt$/ }
	
#### Using Custom Functions
If your object query can **NOT** be achieved using the 'is equal' or regular
expression method or a combination of those two. You also have the option of
custom validation function. For example, if you what to match an object based
on a numeric range you can write your own matching algorithm. An example of
a query argument with a custom function is:

	{ age: function (value, key, object) {
	         if (value >= 18 && value <= 40) {
	           return true;
	         }
	         return false;
	       }
	}

#### Combining methods
For more complex object matching logic, you can combine any of the matching
methods in a single query. Let's assume we want to get the first 5 images in a
document that are 'draggable', have a base URI which include a path segment
'/images/' and whose width is in the range of 16 and 64 pixels.

	require(["cbtree/store/util/QueryEngine"], function (QueryEngine) {
	  var images = Array.prototype.slice.call( document.images );
	  var query  = { draggable: true, 
	                 baseURI:/\/images\//,
	                 width: function (value, key, object ) {
	                          if (value >= 16 && value <= 64) {
	                            return true;
	                          }
	                        }
		             };
	  var queryFunc = QueryEngine( query, {count:5} );
	  var results   = queryFunc( images );
	           ...
	});

#### Case sensitivity
By default, property values are matched case sensitive however, depending on 
then matching method used, you can turn case sensitive matching off. If you use 
the standard 'is equal' method you can set the query options property 
**_ignoreCase_** to true. If set, the **_ignoreCase_** option is applied to
ALL property values that support a toLowerCase() method.

	var query   = { name:["homer", "marge"] };
	var options = { ignoreCase:true };
	QueryEngine( query, options );

If you use regular expressions you can simply add the RegEx flag 'i'

	var query   = { name:/^(homer|marge)$/i };
	QueryEngine( query );





<h2 id="wiki-querying-arrays">Querying Arrays</h2>
The cbtree Query Engine provide full support for querying object properties
whose value is an array. In addition, as shown in the ABNF notation, query 
object properties can also be specified as an array of values or regular 
expression or a combination of those. This section details how the cbtree
Query Engine deals with arrays and array values. To better explain the Query
Engine behavior let's assume we have two store objects defined as follows:

	var myObjects = [
	  { name:"Homer", age: 42, parents:["Abe", "Mona"], hair: "none",  children:["Bart", "Lisa", "Maggie"] }
	  { name:"Ned"  , age: 40, parents:["Root"],        hair: "brown", children:["Rod", "Tod"] }
	];
	
The object properties _name_, _age_ and _hair_ are all single value properties
whereas _parents_ and _children_ are both value array properties.

1. If the object property is a **single** value property and the query object 
	property value is an **array**, a logical OR operation is performed.  
	For example, the next query returns both objects:

	QueryEngine( { name:["Homer","Ned"] } )( myObjects );

2. If the object property is an value **array** and the query object
	property value is a **single** value, then only those objects are returned
	whose property value **array** contain the query object property value.
	For example, the following query only returns the first object
	
	QueryEngine( { children:"Bart" } );

3. If both the object property and query object property are **arrays**, a logical
	AND operations is performed. That is, only those objects are returned whose
	property value **array** contains **ALL** values in the query object value
	array. For example, the next query only returns the first object:
	
	QueryEngine( { children:["Bart", "Lisa"] } )( myObjects );
	
	Whereas the next query returns no objects:
	
	QueryEngine( { children:["Bart", "Lisa", "Ned"] } )( myObjects );

4. If the object property value is an **array** and the query object property
	value is a **regular expression** than any objects whose property value
	**array** contain at least one value that matches the regular expression is returned. 
	For example, the following query returns both objects:

	QueryEngine( { children:/(L|R)/ } )( myObjects );
	

<h2 id="wiki-querying-a-store">Querying a Store</h2>
All examples listed above invoke the Query Engine directly. However, all cbtree
stores use the Query Engine functionality to implement the **cbtree/store/api/Store.query()**
function. Therefore, whenever dealing with a store, you should simply call the
store's query() function to query the store objects. The arguments passed to the
store query() function are the same as those passed to the Query Engine.
See the [Store API](wiki/Store-API#wiki-query) for more details.

The following is a simple example how to use the store query function:

	require(["cbtree/store/Hierarchy"], function ( Hierarchy ) {
	  var store  = new Hierary( {url:"./Simpsons.json", multiParented: true} );
	  var query  = { parent:"Homer", hair:"blond", checked:true };
	  var result = store.query( query );

	  result.forEach( function ( child ) {
	    console.log( "Checked child: " + child.name );
	  });
	}

The example above creates a cbtree Hierarchy store and displays all children
whose parent include "Homer", have "blond" hair and whose checked state is true.


<h2 id="wiki-sample-data">Sample Data</h2>
All examples included on this page use the same dataset as shown below.
The data is a JSON encoded file called 'Simpsons.json': 

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


