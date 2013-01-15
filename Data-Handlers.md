# Data Handlers

Store data handlers allow you to load any arbitray data format into the cbtree stores
with the exception of the FileStore which does not support data handlers.
In essence a data handler is a JavaScript function, called with just one argument,
which converts the input data into an array of JavaScript key:value pairs objects.
The data handlers act upon data passed to the store using either the stores _data_ or
_url_ property.

The signature or prototype of a data handler is a s follows:

#### dataHandler( response )
> Convert the raw data contained by the _reponse_ argument into an array of JavaScript
> key:value pairs object (hash). The array of objects is returned as the result of this
> function.

**_response:_**
> **_TYPE_**: Object

> A JavaScript key:value pairs object with at least one of the following properties:

> - data
> - text

**returns:**
> An array of JavaScript key:value pairs objects. If the _response_ arguments
> holds no data or if the input data failed to satisfy any data constraints,
> if any, an empty array is returned. If a fatal error is encountered it is at
> the discretion of the data handler if it throws an exception or returns an empty
> array.

**example:**

	function dataHandler( response ) {
	  var rawData   = response.data || response.text;
	  var storeData = [];

	  // Convert the rawData here
	             ...

	  return storeData;
	}

### Store Properties
The cbtree stores have two properties to enable a custom data handler, _handleAs_ and
_dataHandler_.
The first, _handleAs_, defines the data type or format the data handler is capable of
converting. The second, _dataHandler_, is the actual data handler function or object.
See the [store API](Store-API#datahandler) for more details.

If you want to use any of the default **dojo/request** data handlers such as "json"
you must **NOT** specify the store property _dataHandler_ when creating a store otherwise
you would override the standard dojo JSON handler. Instead, only specify the property
_handleAs_ as "json".

### Data Handler Declaration
The simplest form of declaring a Data Handler as a loadable AMD module is as follows:

	declare([], function () {
	  return function ( response ) {
	    var rawData   = response.data || response.txt;
	    var storeData = [];

	    // Convert the rawData here
	             ...
	    return storeData;
	  };
	});


<h2 id="handler-registration">Handler Registration</h2>
Every custom data handler must be registered with **_/dojo/request/handlers_** in order to
take effect. To do so you must also specify the store property _handleAs_ as a symbolic name
which will be associated with the data handler and serves as the handlers "data type", for
example: "csv" or "xml".
There are to ways of registering your data handler:

1. Indirect, using the store _dataHandler_ and _handleAs_ properties or,
2. Direct registration with _/dojo/request/handlers_

The first example shows how to register your data handler using the store's _dataHandler_
and _handleAs_ properties.

	require(["cbtree/store/ObjectStore",     // Eventable Object Store with Hierarchy
	         "./myDataHandler"               // Your custom Data Handler.
	        ], function( ObjectStore, myHandler) {

	  // Create an object store and register a custom data handler.
	  var store = new ObjectStore( { url:"/your/data/file/here",
	                                 handleAs: "yourDataType",
	                                 dataHandler: myHandler
                                 });
	             ...
	});

The next example demonstrates direct registration with **_/dojo/request/handlers_**. In this
case, after registration, we can simply tell the store how to handle the data using the handler's
data type.

	require(["dojo/request/handlers",
	         "cbtree/store/ObjectStore",     // Evented Object Store with Hierarchy
	         "./myDataHandler"               // Your custom Data Handler.
	        ], function ( dojoHandlers, ObjectStore, myHandler) {

	  // Register data handler with /dojo/request/handlers
	  dojoHandlers.register("myDataType", myHandler );

	  var store = new ObjectStore( { url:"/your/data/file/here",
	                                 handleAs: "myDataType"
                                 });
	             ...
	});

Regardless of which method you use to register your data handler, once the handler is
registered it is also available to dojo/request and dojo/request/xhr as shown in the
following example:

	require(["dojo/request",
	         "dojo/request/handlers",
	         "cbtree/store/ObjectStore",     // Evented Object Store with Hierarchy
	         "./myDataHandler"               // Your custom Data Handler.
	        ], function ( request, dojoHandlers, ObjectStore, myHandler) {

	  // Register data handler with /dojo/request
	  dojoHandlers.register("myDataType", myHandler );

	  var store = new ObjectStore( { url:"/your/data/file/here",
	                                 handleAs: "myDataType"
                                 });
	             ...

	  var result = request("/some/other/file", {handleAs:"myDataType"});
	  result.then( function (convertedData) {
	             ...
	  });

	});



<h2 id="advanced-data-handler">Advanced Data Handler</h2>
The examples shown above are basic data handlers which can create the required plain
JavaScript objects using the input data. It is assumed the input data holds both the property
names (keys) and associated values. However, many times you will have to supply the object
property names a different way. For example, the following dataset is a simple
comma-separated-value (CSV) set without any object property name information:

	Lisa  , blond, true
	Bart  , blond, true
	Maggie, blond, true
	Patty , blond, true
	Selma , blond, false
	Rod   , blond, true
	Todd  , blond, true

The first column corresponds to the object's _name_, the second to its _hair_ color
and the third its _checked_ state. In order to solve this problem we must provide a
scope, or closure, in which our data handler will operate. The following is a simple
example of how you could do this:

	declare([], function () {

	  // Define the data handler scope/closure

	  function scopedHandler() {

	    this.propNames = ["name", "hair", "checked"];
	    this.delimiter = ",";
	    this.newLine   = "\r\n";

	    var self = this;

	    // Next define the data handler as a property of scopedHandler.

	    this.handler = function ( response ) {
	      var rawData   = response.data || response.txt;
	      var storeData = [];

	      var dataLines = rawData.split( self.newLine );
	      var propNames = self.propNames;

	      dataLines.forEach( function( line ) {
	        var values  = line.split( self.delimiter );
	        var dataObj = {};

	        values.forEach( function( value, index ) {
	          dataObj[propNames[index]] = value;
	        });
	        storeData.push( dataObj );
	      });
	      return storeData;

	    }; /* end data handler */

	  }; /* end data handler scope/closure */

	  return scopedHandler;

	});

Because the actual data handler is now a property of _scopedHandler_ we will have to
create an instance of _scopedHandler_ before we can register the data handler.
Lets assume the above code snippet is stored in a local file named myCsvHandler.js

	require(["cbtree/store/ObjectStore",     // Eventable Object Store with Hierarchy
	         "./myCsvHandler"                // Your CVS Data Handler.
	        ], function( ObjectStore, myCsvHandler) {

	  // Instantiate the data handler...
	  var csvConverter = new myCsvHandler();

	  // Create an object store and register a custom data handler.
	  var store = new ObjectStore( { url:"/your/data/file/here",
	                                 handleAs: "csv",
	                                 dataHandler: csvConverter.handler
                                 });
	             ...
	});

To make it even simpler, the cbtree stores are able to inspect the _dataHandler_ property
and instantiate it if required. Therefore, you could simply write:

	require(["cbtree/store/ObjectStore",     // Eventable Object Store with Hierarchy
	         "./myCsvHandler"                // Your CVS Data Handler.
	        ], function( ObjectStore, myCsvHandler) {

	  // Create an object store and register a custom data handler.
	  var store = new ObjectStore( { url:"/your/data/file/here",
	                                 handleAs: "csv",
	                                 dataHandler: csvConverter
                                 });
	             ...
	});

However, in this case the cbtree store explicitly looks for the property name **_handler_**.
For additional information on the usage of the stores _dataHandler_ and _handleAs_ properties
please refer to the [store API](Store-API#datahandler).

For a complete example of a data handler that fully exploits the data handler scope see
**_/cbtree/store/handlers/csvHandler.js_** and the cbtree demos **_tree10.html_** and
**_tree11.html_**.




