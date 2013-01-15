# Store

The CheckBox Tree package comes with a set stores and a store wrapper. Each store
implements the <strong>cbtree/store/api/Store</strong> API or parts thereof.
The <strong>cbtree/store/api/Store</strong> API is an extension to the
<strong>dojo/store/api/Store</strong> API, as a result, the usage of these stores is
not limited to a CheckBox Tree environment.

<h2 id="store-types">Store Types</h2>
All cbtree stores are in-memory stores, that is, the store content is loaded in memory
without the support of persistent storage. The following store type are available.

* [Memory](#the-memory-store)
* [Natural](#the-natural-store)
* [Hierarchy](#the-hierarchy-store)
* [ObjectStore](#the-object-store)
* [FileStore](#the-file-system-store)

The <strong>cbtree/store/Memory</strong> store serves as the base class for the
Natural, Hierarchy and Object stores, therefore all features available with the
Memory store are also provided by the Natural, Hierarchy and Object stores.

<h3 id="the-memory-store">The Memory Store</h3>

The <strong>cbtree/store/Memory</strong> store is a simple in-memory store similar
to the dojo/store/Memory store with the following extensions:

1. Loading data using a Universal Resource Location (URL).
2. Allow for pre-processing of loaded data using custom [data handlers](#data-handlers).
3. Deferred data loading when using URL's.
4. Apply default properties to store objects.
5. Full support for array property values, including querying.
6. Case insensitive queries and sorting.

The <strong>cbtree/store/Memory</strong> implements the following cbtree/store/api/Store
properties:

* autoLoad
* data
* dataHandler
* defaultProperties
* handleAs
* idProperty
* queryEngine
* url

The <strong>cbtree/store/Memory</strong> implements the following cbtree/store/api/Store
API methods:

* add()
* get()
* getIdentity()
* isItem()
* load()
* put()
* query()
* ready()
* remove()

See the [cbtree/store/api/Store](Store-API) API for a detailed description.

<h3 id="the-natural-store">The Natural Store</h3>

The <strong>cbtree/store/Natural</strong> store extends the Memory store by adding
support for the cbtree/store [PutDirectives](Store-API#PutDirectives) property "before" allowing for store
objects to be arranged in a natural order. The before property value is either an
object or object identifier.

	require( ["cbtree/store/Natural"], function (Natural) {
		var myStore = new Natural( {idProperty:"name"} ));
				...
		myStore.put( {name:"Bart", lastName:"Simpson"} );
				...
		myStore.put( {name:"Lisa", lastName:"Simpson"}, {before:"Bart"} );
	}

The <strong>cbtree/store/Natural</strong> serves as the base class for the Hierarchy store.

<h3 id="the-hierarchy-store">The Hierarchy Store</h3>

The <strong>cbtree/store/Hierarchy</strong> store extends the Natural store by adding
support for the cbtree/store [PutDirectives](Store-API#PutDirectives) "parent" property.
Objects loaded into the store will automatically get a "parent" property if they don't already have one.
The object's parent property value is the identifier, or an array of identifiers, of the
object's parent(s). The store maintains the hierarchy and natural order amongst the store
objects.

As indicated above, the object's parent property value can be an array of parent ids enabling
support for so-called multi-parented objects.

	require( ["cbtree/store/Hierarchy"], function (Hierarchy) {
		var myStore = new Hierarchy( {idProperty:"name", multiParented:true} ));
				...
		myStore.put( {name:"Homer", lastName:"Simpson"} );
		myStore.put( {name:"Marge", lastName:"Simpson"} );
		myStore.put( {name:"Bart", lastName:"Simpson"} );
				...
		myStore.put( {name:"Lisa", lastName:"Simpson"}, {parent:["Homer","Marge"], before:"Bart"} );
	}

Because the Hierarchy store is derived from the Natural store it automatically
inherits the ability to maintain a natural store order as shown in the above example.

The <strong>cbtree/store/Hierarchy</strong> implements the following additional cbtree/store/api/Store
properties:

* hierarchical
* indexChildren
* multiParented
* parentProperty

The <strong>cbtree/store/Hierarchy</strong> implements the following cbtree/store/api/Store
API methods:

* addParent()
* hasChildren()
* getChildren()
* getParents()
* removeParent()

For more detailed information on the Hierarchy Store click [here](Hierarchy-Store)

<h3 id="the-object-store">The Object Store</h3>

The <strong>cbtree/store/ObjectStore</strong> is similar to the Hierarchy store but with
build-in event capabilities. This store type is the preferred store when multiple tree
models operate on a single store.

See also [Eventable](#eventable).

<h3 id="the-file-system-store">The File System Store</h3>

The <strong>cbtree/store/FileStore</strong> retrieves file and directory information
from a back-end server which is then cached as an in-memory object store.
The store allows applications to add custom properties to file object not provided by
the server side application such as a checked state. The in-memory FileStore is
dynamic in that file object may be added, removed or change based on the responses
received from the back-end server.

The <strong>cbtree/store/FileStore</strong> implements the following cbtree/store/api/Store
API methods:

* get()
* getChildren()
* getIdentity()
* isItem()
* load()
* put()
* query()
* ready()
* remove()

For more detailed information on the FileStore click [here](File-Store)

<h2 id="query-engine">Query Engine</h2>

Each store that inherits from the Memory Store will, by default, use the cbtree
[Query Engine](QueryEngine) to search and query store objects. A separate
wiki page is available describing the API, functionality and capabilities of the
_/cbtree/store/util/QueryEngine_. You can however, provide your own query engine
using the store **_queryEngine_** property as long as the query engine complies
with the cbtree query engine API.

<h2 id="eventable">Eventable</h2>

Eventable is a store wrapper which will enable the generation of events whenever the
content of a store changes. Events are emitted for any of the following store operations:

* add
* put
* remove

A store event is a JavaScript key:value pairs object with the following general format:

	store-event = { type: event-name, item: object [,oldItem: object] }
	even-name = "new" | "change" | "delete"

To listen for store events the application has to register an event listener using either
the store's on() method or the dojo/on on() method. For example, if you want to get
notified of new objects being added to the store consider the following example:

	require( ["cbtree/store/Memory",
	          "cbtree/store/Eventable"], function (Memory, Eventable) {

				...

		var myStore = Eventable( new Memory( {idProperty:"name", data: myData} ));
		myStore.on( "new", function (event) {
			console.log( "Object with id: " + this.getIdentity( event.item ) + " was added");
		});

				...

		myStore.put( {name:"Lisa", lastName:"Simpson", hair:"blond"} );

	}

Any store that exposes the dojo/store/api/Store API can be made Eventable creating an
easy to use store notification system which carries alot less overhead than an observable
store does. (See the [observable](#observable) section below).

The cbtree/store/ObjectStore already has build-in event capabilities therefore wrapping
this type of store with Eventable has no effect. From a functional standpoint the following
two statements have the same effect:

	myStore = Eventable( new Hierarchy( ...) );
	myStore = new ObjectStore( ... )

However, the latter, offers better performance and carries less overhead.

<h2 id="observable">Observable</h2>

<strong>dojo/store/Observable</strong> is an object store wrapper that adds support for
notification of data changes to query result sets. The query result sets returned from
a Observable store will include a observe function that can be used to monitor for changes.

Whenever the store content changes, the new, updated or deleted object is run by every
previous query result that have registered listeners. If the object affects the query
result the listener is called.

	require( ["cbtree/store/Memory",
	          "dojo/store/Observable"
	          "dojo/when"], function (Memory, Observable, when) {

				...

		var myStore = Observable( new Memory( {idProperty:"name", data: myData} ));
		var result = myStore.query( {hair:"blond"} );
		result.observe( function (obj, removedFrom, insertedInto) {
		  if (removedFrom == -1) {
			when (result, function () {
			  console.log( "Object with id: " + this.getIdentity( obj ) + " was added");
			});
		  }
		}, true);
				...

		myStore.put( {name:"Lisa", lastName:"Simpson", hair:"blond"} );

	}

All cbtree stores can be made observable however, because the cbtree Object Store already
has event capabilities build-in it doesn't make sense to make it also observable. If you
need an observable store that support data hierarchy use the [Hierarchy](#the-hierarchy-store)
store instead of the Object store.

### IMPORTANT

Careful considerations should be made when selecting an observable store in a CheckBox Tree
environment. As the checked state of any tree node is maintained in the store, any update
to the object's checked state will result in running the updated object againts all observable
queries. If you have a large tree with many branches each branch represents a single
store query which can severly impact the tree performance.


<h2 id="store-data-format">Store Data Format</h2>

By default, each store is loaded with plain JavaScript key:value pairs objects (hash)
which are either passed to the store constructor as the "data" keyword argument or by
means of a URL using the "url" keyword argument. Either way, the store expects the
data to be an array of plain objects.
The following is a valid object array:

	var myData = [
		{ name:"Homer", lastName:"Simpson", hair:"none" },
		{ name:"Marge", lastName:"Simpson", hair:"blond" },
		{ name:"Bart", lastName:"Simpson", hair:"blond" }
	];

	var myStore = new Memory( {data:myData} );

However, when loading data using a URL the data must be JSON encoded, that is, all key
or property names MUST be double-quoted strings:

	[
		{ "name":"Homer", "lastName":"Simpson", "hair":"none" },
		{ "name":"Marge", "lastName":"Simpson", "hair":"blond" },
		{ "name::"Bart", "lastName":"Simpson", "hair":"blond" }
	]

Note that the proprty names <em><strong>name, lastName</strong></em> and <em><strong>hair</strong></em>
are all enclosed in double quotes.

<h2 id="data-handlers">Data Handlers</h2>

All cbtree stores, with the exception of the FileStore, offer the option of pre-processing
data using either one of the default <strong>dojo/request</strong> handlers or registering
a custom data handler. Typically, a data handler takes an arbitrary data format and converts
the data returning an array of plain JavaScript key:value pairs objects ready for consumption
by the store loader. (see [Store Data Format](#store-data-format))

This ability eliminates the need having to create a new store for every data format. In addition,
because the stores register any custom data handler with <strong>dojo/request/handlers</strong>
the data handler automatically becomes available to the <strong>dojo/request</strong> module.

The cbtree stores comes with two sample data handlers:

1. Comma Separated Value (CSV) data handler (csvHandler.js)
2. ItemFileReadStore data handler (ifrsHandler.js)

The CSV data handler enables you to load rfc1480 compliant comma-separated-value files or objects
without any further processing. The following example demonstrates how to use a custom data handler
with the cbtree stores:

	require(["cbtree/store/ObjectStore",		 // Eventable Object Store with Hierarchy
			 "cbtree/store/handlers/csvHandler"	 // CSV Data Handler.
			], function( ObjectStore, csvHandler) {

		// Create an object store and register a custom data handler.
		var store = new ObjectStore( { url:"../../store/csv/Simpsons.csv",
									   idProperty:"name",
									   handleAs: "csv",
									   dataHandler: {
										 handler: csvHandler,
										 options: {
											fieldNames:["name", "parent", "hair", "checked"],
											trim:true
										 }
									  }
									});

See also <strong>cbtree/demos/store/tree11.html</strong> for an alternative way of registring
a custom data handler.

If you need to convert any legacy <strong>dojo/data/ItemFileReadStore</strong> formatted data
consider to following example using the ifrsHandler.

	require(["cbtree/store/ObjectStore",		 // Eventable Object Store with Hierarchy
			 "cbtree/store/handlers/ifrsHandler" // ItemFileReadStore Data Handler.
			], function( ObjectStore, ifrsHandler) {

		// Create an object store and register a custom data handler.
		var store = new ObjectStore( { url:"../../store/json/Simpsons_IFRS.json",
									   idProperty:"name",
									   handleAs: "ifrs",
									   dataHandler: ifrsHandler
									 }
									});

Notice the above example uses the short notation for the dataHandler property.
