
The CheckBox Tree package comes with a set of stores and a store wrapper. 
Each store implements the [cbtree/store/api/Store](Store-API) API or parts thereof.
The **cbtree/store/api/Store** API is an extension to the
**dojo/store/api/Store** API, as a result, the usage of these stores is
not limited to a CheckBox Tree environment.

Before selecting the store you want to use in a CheckBox Tree environment it is
important to understand the pros and cons of each of them. Please read the 
[store selection](Model#wiki-selecting-a-store)
section of the cbtree models.

<h3>Content <span class="mega-octicon octicon-book"></span></h3>
* [Store Types](#store-types)
* [Eventable](#eventable)
* [Observable](#observable)
* [Data Format](#data-format)
* [Data Filter](#data-filter)
* [Data Handlers](#data-handlers)
* [CORS Support](#cors-support)
* [Reloading a Store](#reloading-a-store)
* [Store API Maxtrix](#store-api-matrix)


<h2 id="store-types">Store Types</h2>
All cbtree stores are in-memory stores, that is, the store content is loaded in
memory without the support of persistent storage. Any changes to the store are
lost after your session is terminated. The following store type are available.

* [Memory](#the-memory-store)
* [Natural](#the-natural-store)
* [Hierarchy](#the-hierarchy-store)
* [ObjectStore](#the-object-store)
* [FileStore](#the-file-system-store)

The **cbtree/store/Memory** store serves as the base class for the
Natural, Hierarchy and Object stores, therefore all features available with the
Memory store are also provided by the Natural, Hierarchy and Object stores.
The basic store inheritance is as follows:

> Store-API < Memory < Natural < Hierarchy < ObjectStore

> Store-API < FileStore

<h2 id="the-memory-store">The Memory Store</h2>

The **cbtree/store/Memory** store is a simple in-memory store similar
to the dojo/store/Memory store with the following enhancements:

1. Loading data using a Universal Resource Location (URL).
2. Allow for pre-processing of loaded data using custom [data handlers](#data-handlers).
3. Deferred data loading and filtering.
4. Apply default properties to store objects.
5. Enhanced [Query Engine](Query-Engine)

The **cbtree/store/Memory** store serves as the base class for the
Natural, Hierarchy and Object stores.

```javascript
require( ["cbtree/store/Memory"], function (Memory) {
  var myStore = new Memory( {data:myData} ));
      ...
}
```


<h2 id="the-natural-store">The Natural Store</h2>

The **cbtree/store/Natural** store extends the Memory store by adding support for
the cbtree/store [PutDirectives](Store-API#wiki-PutDirectives) **_"before"_** property
allowing for store objects to be arranged in a natural order.
The **_before_** property value is either an object or object identifier.

```javascript
require( ["cbtree/store/Natural"], function (Natural) {
  var myStore = new Natural( {idProperty:"name"} ));
      ...
  myStore.put( {name:"Bart", lastName:"Simpson"} );
      ...
  myStore.put( {name:"Lisa", lastName:"Simpson"}, {before:"Bart"} );
}
```

The **cbtree/store/Natural** serves as the base class for the Hierarchy store.






<h2 id="the-hierarchy-store">The Hierarchy Store</h2>

The **cbtree/store/Hierarchy** store extends the Natural store by adding
support for the cbtree/store [PutDirectives](Store-API#wiki-PutDirectives) 
**_"parent"_** property.
Objects loaded into the store will automatically get a **_parent_** property
if they don't already have one.
The object's parent property value is the identifier, or an array of identifiers, 
of the object's parent(s). The store maintains the hierarchy and natural order
amongst the store objects.

As indicated above, the object's parent property value can be an array of parent 
ids enabling support for so-called multi-parented objects.

```javascript
require( ["cbtree/store/Hierarchy"], function (Hierarchy) {
  var myStore = new Hierarchy( {idProperty:"name", multiParented:true} ));
      ...
  myStore.put( {name:"Homer", lastName:"Simpson"} );
  myStore.put( {name:"Marge", lastName:"Simpson"} );
  myStore.put( {name:"Bart", lastName:"Simpson"} );
      ...
  myStore.put( {name:"Lisa", lastName:"Simpson"}, {parent:["Homer","Marge"], before:"Bart"} );
}
```

Because the Hierarchy store is derived from the Natural store it automatically
inherits the ability to maintain a natural store order as shown in the above example.

See the [Hierarchy Store](Hierarchy-Store) page for more detailed information.







<h2 id="the-object-store">The Object Store</h2>

The **cbtree/store/ObjectStore** is similar to the Hierarchy store but with
build-in event capabilities. This store type is the preferred store when multiple
tree models operate on a single store or when content is added or removed
dynamically.

```javascript
require( ["cbtree/store/ObjectStore"], function (ObjectStore) {
  var myStore = new ObjectStore( {idProperty:"name", multiParented:true} ));
  myStore.on( "new", function( event ) {
    console.log( "Item with id: "+ this.getIdentity(event.item) + " added.");
  });
      ...
}
```

See also [Eventable](#eventable).

<h2 id="the-file-system-store">The File System Store</h2>

The **cbtree/store/FileStore** retrieves file and directory information
from a back-end server which is then cached as an in-memory object store.
The store allows applications to add custom properties to file object not provided by
the server side application such as a checked state. The in-memory FileStore is
dynamic in that file object may be added, removed or change based on the responses
received from the back-end server.

See the [File Store](File-Store) page for detailed information.







<h2 id="eventable">Eventable</h2>

Eventable is a store wrapper enabling event generation whenever the content of a
store changes. An eventable store does **NOT** rely on, or even care about, previously
executed queries like an Observable store does. It merely emit notifications when
the store content changes. Events are emitted for any of the following store operations:

* add
* put
* remove

A cbtree store event is a JavaScript key:value pairs object with the following
ABNF notation:

	store-event = "{" "type:" event-name "," "detail:" "{" "item:" object ["," "oldItem:" object] "}" "}"
	event-name  = "new" / "change" / "delete"

To listen for store events the application has to register an event listener using
either the store's `on()` method or the dojo/on `on()` method. For example, if
you want to get notified of new objects being added to the store consider the
following example:

```javascript
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
```

Any store that exposes the [cbtree/store/api/Store](Store-API) API can be made
Eventable creating an easy to use store notification system which carries alot
less overhead than an observable store does. (See the [observable](#observable)
section below).

The cbtree/store/ObjectStore already has build-in event capabilities therefore wrapping
this type of store with Eventable has no effect. From a functional standpoint the following
two statements have the same effect:

	myStore = Eventable( new Hierarchy( ...) );
	myStore = new ObjectStore( ... )

However, the latter, offers better performance and carries less overhead.
For more details on event handling see the [working with events](CheckBox-Tree-Usage#working-with-events)
section.






<h2 id="observable">Observable</h2>

**dojo/store/Observable** is an object store wrapper that adds support for
notification of data changes to query result sets. The query result sets returned
from a Observable store will include a `observe()` function that can be used to
monitor the query result for any changes due to changes in the store.

Whenever the store content changes, the new, updated or deleted object is run by every
previous query result that have registered listeners. If the object affects the query
result the listener is called.

```javascript
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
```

All cbtree stores can be made observable however, because the cbtree Object Store
already has event capabilities build-in it doesn't make sense to make it also
observable. If you need an observable store that support data hierarchy use the 
[Hierarchy](#the-hierarchy-store) store instead of the Object store.

### IMPORTANT

Careful considerations should be made when selecting an observable store in a
CheckBox Tree environment. As the checked state of any tree node is maintained
in the store, any update to the object's checked state will result in running
the updated object againts **ALL** observable queries.
If you have a large tree with many branches, keep in mind that each branch
represents a single store query, it may severly impact the tree performance.
In addition, if you use a tree model with its [checkedStrict](Model-API#wiki-checkedstrict)
property set to true, each tree node represents a store query.

**From a performance stand point the cbtree Object Store or an Eventable Store
is the better choice.**







<h2 id="data-format">Data Format</h2>

By default, each store is loaded with plain JavaScript key:value pairs objects (hash)
which are either passed to the store constructor as the "data" keyword argument or by
means of a URL using the "url" keyword argument. Either way, the store expects the
data to be an array of plain objects.
The following is an example of a valid object array:

```javascript
var myData = [
  { name:"Homer", lastName:"Simpson", hair:"none" },
  { name:"Marge", lastName:"Simpson", hair:"blue" },
  { name:"Bart" , lastName:"Simpson", hair:"blond" }
];

var myStore = new Memory( {data:myData} );
```

When loading data using a URL the data (file content) must be **JSON** encoded,
that is, as of dojo 1.8 all key or property names MUST be double-quoted strings. 
For the JSON encoding rules please refer to [http://www.json.org](http://www.json.org/).

```
[
  { "name":"Homer", "lastName":"Simpson", "hair":"none" },
  { "name":"Marge", "lastName":"Simpson", "hair":"blue" },
  { "name":"Bart" , "lastName":"Simpson", "hair":"blond" }
]
```

Note that the proprty names **_name, lastName_** and **_hair_** are all enclosed in double
quotes.

However, if your data is not a plain array of JavaScript objects the cbtree 
stores still offers the options to load it using a custom data handler. For
details refer to the [Data Handler](#data-handlers) section below.





<h2 id="data-filter">Data Filter</h2>
If you are working with large and potentically complex data sets it may be useful
to only load those objects in the store that are really required by your application.
For example, you have a large file with geographical data but your application
only need those records identifying capital cities.  
Rather than loading the entire file with thousands of records you can pre-filter
the ones you really need using the store [filter](Store-API#wiki-filter)
property.

This approach has a number of advantages:

1. Less memory used.
2. Simpler Store queries.
3. Store queries execute faster.

For example, consider the following:

```javascript
var myStore = new Hierarchy( {url:"some/large/file", filter:{type:"capital"}, ... } }; 
```

Please note that if the store `filter` property is set, the filter is applied 
**_after_** any data handler is called.

<h2 id="data-handlers">Data Handlers</h2>
If your data does not comply with the required [Data Format](#data-format),
the cbtree stores offer the option of so-called data handlers. All cbtree stores, 
with the exception of the FileStore, offer the option of pre-processing data, before
populating the store, using either one of the default **dojo/request** handlers
or registering a custom data handler.
Typically, a data handler takes an arbitrary data format and converts the data
returning an array of plain JavaScript key:value pairs objects ready for
consumption by the store loader.

This ability eliminates the need of having to create a new store for every data 
format or having to handle XHR requests yourself. In addition, because the stores
registers any custom data handler with **dojo/request/handlers** the data handler
automatically becomes available to the **dojo/request** module.

The cbtree stores comes with three sample data handlers:

1. Comma Separated Value (CSV) data handler (csvHandler.js)
2. ItemFileReadStore data handler (ifrsHandler.js)
3. [ArcGIS Geocoder](http://help.arcgis.com/en/webapi/javascript/arcgis/jssamples/#sample/locator_simple) data handler (arcGisHandler.js)

All sample handlers can be found at **_/cbtree/store/handlers/_**

The CSV data handler enables you to load [rfc4180](http://datatracker.ietf.org/doc/rfc4180/)
compliant comma-separated-value files or objects without any further processing.
The following example demonstrates how to use a custom data handler with the
cbtree stores:

```javascript
require(["cbtree/store/ObjectStore",         // Eventable Object Store with Hierarchy
         "cbtree/store/handlers/csvHandler"   // CSV Data Handler.
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
```

If you need to convert any legacy **dojo/data/ItemFileReadStore** formatted data
consider to following example using the ifrsHandler.

```javascript
require(["cbtree/store/ObjectStore",     // Eventable Object Store with Hierarchy
         "cbtree/store/handlers/ifrsHandler" // ItemFileReadStore Data Handler.
        ], function( ObjectStore, ifrsHandler) {

  // Create an object store and register a custom data handler.
  var store = new ObjectStore( { url:"../../store/json/Simpsons_IFRS.json",
                                 idProperty:"name",
                                 handleAs: "ifrs",
                                 dataHandler: ifrsHandler
                               }
                             });
```

For a detailed description of the implementation of a custom Data Handler please
refer to the [Data Handler](Data-Handlers) wiki page.







<h2 id="cors-support">CORS Support</h2>
Cross-Origin Resource Sharing (CORS) is a mechanism to allow XMLHttpRequests to
other domains. By default, cross-domain requests are forbidden by web browsers.
CORS defines a way in which the browser and the server can interact to determine
whether or not to allow the cross-origin request.

All cbtree stores, with the exception of the FileStore, can be extended with CORS
support. Please note that CORS support is only required when loading a store
with data retrieved from remote domains.

To extend a cbtree store with CORS support simply load CORS the extension:

```javascript
required(["cbtree/store/Hierarchy",
          "cbtree/store/extensions/CORS"
         ], function ( Hierarchy, CORS ) {

  var myStore   = new Hierarchy( {url:"http://some-remote-site"} );
                  ...
});
```

Once the CORS extension is loaded you can start using URLs with the schema
'http' or 'https'.

<span class="mega-octicon octicon-alert"></span> The remote domain must 
support the [Access-Control-Allow-Origin](http://www.w3.org/TR/cors/#access-control-allow-origin-response-header)
header. See the [CORS standard](http://www.w3.org/TR/cors/) for more details.



<h2 id="Reloading-a-store">Reloading a Store</h2>
Normally store data is loaded during the instantiation of the store and extended
using the store functions `add()` and `put()`. However, under certain circumstances
it may be desirable to flush and reload the store content. All cbtree stores, 
with the exception of the FileStore, offer the ability to dynamically reload and
refresh their content.  
To reload the store it must be closed first calling the store `close()` function 
with either the *clear* parameter set to true, as in `close( true )` or the store
property *clearOnClose* set to true. Once the store is closed and cleared you 
can simply call the store `load()` function with a new dataset or URL to initiate
another store load. For example:
```javascript
var myStore = new Hierarchy( {url:"/file/path/and/file.json", clearOnClose:true} );
                     ...
myStore.close();
                     ...
myStore.load( {url:"/file/path/and/anotherFile.json"} );
```
<span class="octicon octicon-alert"></span>
the store **_must_** be closed first before any new load request can take
effect.

As of release cbtree-v0.9.3 both the cbtree models and tree provide full support
for dynamic store reloads. As soon as a store is closed and cleared the models
enters the so-called *reset* stage forcing the tree to unload all nodes. At this
point in the process both the model and tree will be empty as well.
When the store completes the data load request the model resumes operation 
triggering a reload of the cbtree effectively refreshing the tree with the new
store data.


<h2 id="store-api-matrix">Store API Matrix</h2>

The following table shows which `cbtree/store/api/Store` API [properties](Store-API#wiki-store-properties)
and [functions](Store-API#wiki-store-functions) each store implements.

<table>
	<tr>
		<th>Store API</th>
		<th>Name</th>
		<th>Memory Store</th>
		<th>Natural Store</th>
		<th><a href="Hierarchy-Store">Hierarchy Store</a></th>
		<th>Object Store</th>
		<th><a href="File-Store">File Store</a></th>
	</tr>
	<tr>
		<td rowspan="13">Property</td>
		<td><a href="Store-API#wiki-autoload">autoLoad</a></td>
		<td><span class="octicon octicon-check"></span></td>
		<td><span class="octicon octicon-check"></span></td>
		<td><span class="octicon octicon-check"></span></td>
		<td><span class="octicon octicon-check"></span></td>
		<td><span class="octicon octicon-check"></span></td>
	</tr>
	<tr>
		<td><a href="Store-API#wiki-clearonclose">clearOnClose</a></td>
		<td><span class="octicon octicon-check"></span></td>
		<td><span class="octicon octicon-check"></span></td>
		<td><span class="octicon octicon-check"></span></td>
		<td><span class="octicon octicon-check"></span></td>
		<td></td>
	</tr>
	<tr>
		<td><a href="Store-API#wiki-data">data</a></td>
		<td><span class="octicon octicon-check"></span></td>
		<td><span class="octicon octicon-check"></span></td>
		<td><span class="octicon octicon-check"></span></td>
		<td><span class="octicon octicon-check"></span></td>
		<td></td>
	</tr>
	<tr>
		<td><a href="Store-API#wiki-datahandler">dataHandler</a></td>
		<td><span class="octicon octicon-check"></span></td>
		<td><span class="octicon octicon-check"></span></td>
		<td><span class="octicon octicon-check"></span></td>
		<td><span class="octicon octicon-check"></span></td>
		<td></td>
	</tr>
	<tr>
		<td><a href="Store-API#wiki-defaultproperties">defaultProperties</a></td>
		<td><span class="octicon octicon-check"></span></td>
		<td><span class="octicon octicon-check"></span></td>
		<td><span class="octicon octicon-check"></span></td>
		<td><span class="octicon octicon-check"></span></td>
		<td><span class="octicon octicon-check"></span></td>
	</tr>
	<tr>
		<td><a href="Store-API#wiki-filter">filter</a></td>
		<td><span class="octicon octicon-check"></span></td>
		<td><span class="octicon octicon-check"></span></td>
		<td><span class="octicon octicon-check"></span></td>
		<td><span class="octicon octicon-check"></span></td>
		<td></td>
	</tr>
	<tr>
		<td><a href="Store-API#wiki-handleas">handleAs</a></td>
		<td><span class="octicon octicon-check"></span></td>
		<td><span class="octicon octicon-check"></span></td>
		<td><span class="octicon octicon-check"></span></td>
		<td><span class="octicon octicon-check"></span></td>
		<td></td>
	</tr>
	<tr>
		<td><a href="Store-API#wiki-hierarchical">hierarchical<sup>1</sup></a></td>
		<td></td>
		<td></td>
		<td><span class="octicon octicon-check"></span></td>
		<td><span class="octicon octicon-check"></span></td>
		<td></td>
	</tr>
	<tr>
		<td><a href="Store-API#wiki-idproperty">idProperty</a></td>
		<td><span class="octicon octicon-check"></span></td>
		<td><span class="octicon octicon-check"></span></td>
		<td><span class="octicon octicon-check"></span></td>
		<td><span class="octicon octicon-check"></span></td>
		<td></td>
	</tr>
	<tr>
		<td><a href="Store-API#wiki-multiparented">multiParented</a></td>
		<td></td>
		<td></td>
		<td><span class="octicon octicon-check"></span></td>
		<td><span class="octicon octicon-check"></span></td>
		<td></td>
	</tr>
	<tr>
		<td><a href="Store-API#wiki-parentproperty">parentProperty</a></td>
		<td></td>
		<td></td>
		<td><span class="octicon octicon-check"></span></td>
		<td><span class="octicon octicon-check"></span></td>
		<td></td>
	</tr>
	<tr>
		<td><a href="Store-API#wiki-queryengine">queryEngine</a></td>
		<td><span class="octicon octicon-check"></span></td>
		<td><span class="octicon octicon-check"></span></td>
		<td><span class="octicon octicon-check"></span></td>
		<td><span class="octicon octicon-check"></span></td>
		<td><span class="octicon octicon-check"></span></td>
	</tr>
	<tr>
		<td><a href="Store-API#wiki-url">url</a></td>
		<td><span class="octicon octicon-check"></span></td>
		<td><span class="octicon octicon-check"></span></td>
		<td><span class="octicon octicon-check"></span></td>
		<td><span class="octicon octicon-check"></span></td>
		<td><span class="octicon octicon-check"></span></td>
	</tr>
</table>

<sup>1</sup> Read-Only property.

<table>
	<tr>
		<th>Store API</th>
		<th>Name</th>
		<th>Memory Store</th>
		<th>Natural Store</th>
		<th><a href="Hierarchy-Store">Hierarchy Store</a></th>
		<th>Object Store</th>
		<th><a href="File-Store">File Store</a></th>
	</tr>
	<tr>
		<td rowspan="16">Function</td>
		<td><a href="Store-API#wiki-add">add<sup>2</sup></a></td>
		<td><span class="octicon octicon-check"></span></td>
		<td><span class="octicon octicon-check"></span></td>
		<td><span class="octicon octicon-check"></span></td>
		<td><span class="octicon octicon-check"></span></td>
		<td></td>
	</tr>
	<tr>
		<td><a href="Store-API#wiki-addparent">addParent</a></td>
		<td></td>
		<td></td>
		<td><span class="octicon octicon-check"></span></td>
		<td><span class="octicon octicon-check"></span></td>
		<td></td>
	</tr>
	<tr>
		<td><a href="Store-API#wiki-close">close</a></td>
		<td><span class="octicon octicon-check"></span></td>
		<td><span class="octicon octicon-check"></span></td>
		<td><span class="octicon octicon-check"></span></td>
		<td><span class="octicon octicon-check"></span></td>
		<td></td>
	</tr>
	<tr>
		<td><a href="Store-API#wiki-destroy">destroy</a></td>
		<td><span class="octicon octicon-check"></span></td>
		<td><span class="octicon octicon-check"></span></td>
		<td><span class="octicon octicon-check"></span></td>
		<td><span class="octicon octicon-check"></span></td>
		<td><span class="octicon octicon-check"></span></td>
	</tr>
	<tr>
		<td><a href="Store-API#wiki-get">get<sup>2</sup></a></td>
		<td><span class="octicon octicon-check"></span></td>
		<td><span class="octicon octicon-check"></span></td>
		<td><span class="octicon octicon-check"></span></td>
		<td><span class="octicon octicon-check"></span></td>
		<td><span class="octicon octicon-check"></span></td>
	</tr>
	<tr>
		<td><a href="Store-API#wiki-getchildren">getChildren<sup>2</sup></a></td>
		<td></td>
		<td></td>
		<td><span class="octicon octicon-check"></span></td>
		<td><span class="octicon octicon-check"></span></td>
		<td><span class="octicon octicon-check"></span></td>
	</tr>
	<tr>
		<td><a href="Store-API#wiki-getidentity">getIdentity<sup>2</sup></a></td>
		<td><span class="octicon octicon-check"></span></td>
		<td><span class="octicon octicon-check"></span></td>
		<td><span class="octicon octicon-check"></span></td>
		<td><span class="octicon octicon-check"></span></td>
		<td><span class="octicon octicon-check"></span></td>
	</tr>
	<tr>
		<td><a href="Store-API#wiki-getparents">getParents</a></td>
		<td></td>
		<td></td>
		<td><span class="octicon octicon-check"></span></td>
		<td><span class="octicon octicon-check"></span></td>
		<td></td>
	</tr>
	<tr>
		<td><a href="Store-API#wiki-haschildren">hasChildren</a></td>
		<td></td>
		<td></td>
		<td><span class="octicon octicon-check"></span></td>
		<td><span class="octicon octicon-check"></span></td>
		<td><span class="octicon octicon-check"></span></td>
	</tr>
	<tr>
		<td><a href="Store-API#wiki-isitem">isItem</a></td>
		<td><span class="octicon octicon-check"></span></td>
		<td><span class="octicon octicon-check"></span></td>
		<td><span class="octicon octicon-check"></span></td>
		<td><span class="octicon octicon-check"></span></td>
		<td><span class="octicon octicon-check"></span></td>
	</tr>
	<tr>
		<td><a href="Store-API#wiki-load">load</a></td>
		<td><span class="octicon octicon-check"></span></td>
		<td><span class="octicon octicon-check"></span></td>
		<td><span class="octicon octicon-check"></span></td>
		<td><span class="octicon octicon-check"></span></td>
		<td><span class="octicon octicon-check"></span></td>
	</tr>
	<tr>
		<td><a href="Store-API#wiki-put">put<sup>2</sup></a></td>
		<td><span class="octicon octicon-check"></span></td>
		<td><span class="octicon octicon-check"></span></td>
		<td><span class="octicon octicon-check"></span></td>
		<td><span class="octicon octicon-check"></span></td>
		<td><span class="octicon octicon-check"></span></td>
	</tr>
	<tr>
		<td><a href="Store-API#wiki-query">query<sup>2</sup></a></td>
		<td><span class="octicon octicon-check"></span></td>
		<td><span class="octicon octicon-check"></span></td>
		<td><span class="octicon octicon-check"></span></td>
		<td><span class="octicon octicon-check"></span></td>
		<td><span class="octicon octicon-check"></span></td>
	</tr>
	<tr>
		<td><a href="Store-API#wiki-ready">ready</a></td>
		<td><span class="octicon octicon-check"></span></td>
		<td><span class="octicon octicon-check"></span></td>
		<td><span class="octicon octicon-check"></span></td>
		<td><span class="octicon octicon-check"></span></td>
		<td><span class="octicon octicon-check"></span></td>
	</tr>
	<tr>
		<td><a href="Store-API#wiki-remove">remove<sup>2</sup></a></td>
		<td><span class="octicon octicon-check"></span></td>
		<td><span class="octicon octicon-check"></span></td>
		<td><span class="octicon octicon-check"></span></td>
		<td><span class="octicon octicon-check"></span></td>
		<td><span class="octicon octicon-check"></span></td>
	</tr>
	<tr>
		<td><a href="Store-API#wiki-removeparent">removeParent</a></td>
		<td></td>
		<td></td>
		<td><span class="octicon octicon-check"></span></td>
		<td><span class="octicon octicon-check"></span></td>
		<td></td>
	</tr>

</table>

<sup>2</sup> Functions defined as part of the default `dojo/store/api/Store` API


<table>
	<tr>
		<th>Extensions<sup>3</sup></th>
		<th>Name</th>
		<th>Memory Store</th>
		<th>Natural Store</th>
		<th><a href="Hierarchy-Store">Hierarchy Store</a></th>
		<th>Object Store</th>
		<th><a href="File-Store">File Store</a></th>
	</tr>
	<tr>
		<td rowspan="6">Properties</td>
		<td><a href="File-Store#wiki-authtoken">authToken</a></td>
		<td></td>
		<td></td>
		<td></td>
		<td></td>
		<td><span class="octicon octicon-check"></span></td>
	</tr>
	<tr>
		<td><a href="Hierarchy-Store#wiki-indexchildren">indexChildren</a></td>
		<td></td>
		<td></td>
		<td><span class="octicon octicon-check"></span></td>
		<td><span class="octicon octicon-check"></span></td>
		<td></td>
	</tr>
	<tr>
		<td><a href="File-Store#wiki-basepath">basePath</a></td>
		<td></td>
		<td></td>
		<td></td>
		<td></td>
		<td><span class="octicon octicon-check"></span></td>
	</tr>
	<tr>
		<td><a href="File-Store#wiki-options">options</a></td>
		<td></td>
		<td></td>
		<td></td>
		<td></td>
		<td><span class="octicon octicon-check"></span></td>
	</tr>
	<tr>
		<td><a href="File-Store#wiki-preventcache">preventCache</a></td>
		<td></td>
		<td></td>
		<td></td>
		<td></td>
		<td><span class="octicon octicon-check"></span></td>
	</tr>
	<tr>
		<td><a href="File-Store#wiki-sort">sort</a></td>
		<td></td>
		<td></td>
		<td></td>
		<td></td>
		<td><span class="octicon octicon-check"></span></td>
	</tr>

	<tr>
		<td rowspan="11">Function</td>
		<td><a href="Ancestry-Extension#wiki-analyze">analyze<sup>4</sup></a></td>
		<td></td>
		<td></td>
		<td><span class="octicon octicon-check"></span></td>
		<td><span class="octicon octicon-check"></span></td>
		<td></td>
	</tr>
	<tr>
		<td><a href="Ancestry-Extension#wiki-getancestors">getAncestors<sup>4</sup></a></td>
		<td></td>
		<td></td>
		<td><span class="octicon octicon-check"></span></td>
		<td><span class="octicon octicon-check"></span></td>
		<td></td>
	</tr>
	<tr>
		<td><a href="Ancestry-Extension#wiki-getdescendants">getDescendants<sup>4</sup></a></td>
		<td></td>
		<td></td>
		<td><span class="octicon octicon-check"></span></td>
		<td><span class="octicon octicon-check"></span></td>
		<td></td>
	</tr>
	<tr>
		<td><a href="Ancestry-Extension#wiki-getpaths">getPaths<sup>4</sup></a></td>
		<td></td>
		<td></td>
		<td><span class="octicon octicon-check"></span></td>
		<td><span class="octicon octicon-check"></span></td>
		<td></td>
	</tr>
	<tr>
		<td><a href="Ancestry-Extension#wiki-getsiblings">getSiblings<sup>4</sup></a></td>
		<td></td>
		<td></td>
		<td><span class="octicon octicon-check"></span></td>
		<td><span class="octicon octicon-check"></span></td>
		<td></td>
	</tr>
	<tr>
		<td><a href="Ancestry-Extension#wiki-isancestorof">isAncestorOf<sup>4</sup></a></td>
		<td></td>
		<td></td>
		<td><span class="octicon octicon-check"></span></td>
		<td><span class="octicon octicon-check"></span></td>
		<td></td>
	</tr>
	<tr>
		<td><a href="Ancestry-Extension#wiki-isbefore">isBefore<sup>4</sup></a></td>
		<td></td>
		<td><span class="octicon octicon-check"></span></td>
		<td><span class="octicon octicon-check"></span></td>
		<td><span class="octicon octicon-check"></span></td>
		<td></td>
	</tr>
	<tr>
		<td><a href="Ancestry-Extension#wiki-ischildof">isChildOf<sup>4</sup></a></td>
		<td></td>
		<td></td>
		<td><span class="octicon octicon-check"></span></td>
		<td><span class="octicon octicon-check"></span></td>
		<td></td>
	</tr>
	<tr>
		<td><a href="Ancestry-Extension#wiki-isdescendantof">isDescendantOf<sup>4</sup></a></td>
		<td></td>
		<td></td>
		<td><span class="octicon octicon-check"></span></td>
		<td><span class="octicon octicon-check"></span></td>
		<td></td>
	</tr>
	<tr>
		<td><a href="Ancestry-Extension#wiki-issiblingof">isSiblingOf<sup>4</sup></a></td>
		<td></td>
		<td></td>
		<td><span class="octicon octicon-check"></span></td>
		<td><span class="octicon octicon-check"></span></td>
		<td></td>
	</tr>
	<tr>
		<td><a href="File-Store#wiki-rename">rename</a></td>
		<td></td>
		<td></td>
		<td></td>
		<td></td>
		<td><span class="octicon octicon-check"></span></td>
	</tr>
</table>

<sup>3</sup> Extension to the `cbtree/store/api/Store` API.  
<sup>4</sup> Requires the [Ancestry](Ancestry) extension `cbtree/store/extensions/Ancestry`
to be loaded.
