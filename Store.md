
The CheckBox Tree package comes with a set stores and a store wrapper. Each store
implements the **cbtree/store/api/Store** API or parts thereof.
The **cbtree/store/api/Store** API is an extension to the
**dojo/store/api/Store** API, as a result, the usage of these stores is
not limited to a CheckBox Tree environment.

Before selecting the store you want to use in a CheckBox Tree environment it is important
to understand the pros and cons of each of them. Please read the 
[store selection](Store-Models#wiki-selecting-a-store)
section of the cbtree models.

<h3>Content <span class="mega-icon mega-icon-readme"></span></h3>
* [Store Types](#store-types)
* [Eventable](#eventable)
* [Observable](#observable)
* [Data Format](#store-data-format)
* [Data Hierarchy](#store-data-hierarchy)
* [Data Handlers](#data-handlers)


<h2 id="store-types">Store Types</h2>
All cbtree stores are in-memory stores, that is, the store content is loaded in memory
without the support of persistent storage. Any changes to the store are lost after
your session is terminated. The following store type are available.

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
to the dojo/store/Memory store with the following extensions:

1. Loading data using a Universal Resource Location (URL).
2. Allow for pre-processing of loaded data using custom [data handlers](#data-handlers).
3. Deferred data loading when using URL's.
4. Apply default properties to store objects.
5. Enhanced [Query Engine](Query-Engine)

The **cbtree/store/Memory** implements the following cbtree/store/api/Store
properties:

* autoLoad
* data
* dataHandler
* defaultProperties
* handleAs
* idProperty
* queryEngine
* url

The **cbtree/store/Memory** implements the following cbtree/store/api/Store
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

See the [cbtree/store/api/Store](Store-API) API for a detailed description of the properties
and functions.

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

The **cbtree/store/Hierarchy** implements the following additional cbtree/store/api/Store
properties:

* hierarchical
* indexChildren
* multiParented
* parentProperty

The **cbtree/store/Hierarchy** implements the following cbtree/store/api/Store
API methods:

* addParent()
* hasChildren()
* getChildren()
* getParents()
* removeParent()

For more detailed information on the Hierarchy Store click [here](Hierarchy-Store)

<h2 id="the-object-store">The Object Store</h2>

The **cbtree/store/ObjectStore** is similar to the Hierarchy store but with
build-in event capabilities. This store type is the preferred store when multiple tree
models operate on a single store.

See also [Eventable](#eventable).

<h2 id="the-file-system-store">The File System Store</h2>

The **cbtree/store/FileStore** retrieves file and directory information
from a back-end server which is then cached as an in-memory object store.
The store allows applications to add custom properties to file object not provided by
the server side application such as a checked state. The in-memory FileStore is
dynamic in that file object may be added, removed or change based on the responses
received from the back-end server.

The **cbtree/store/FileStore** implements the following cbtree/store/api/Store
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

<h2 id="eventable">Eventable</h2>

Eventable is a store wrapper enabling event generation whenever the content of a
store changes. An eventable store does **NOT** rely on, or even care about, previously
executed queries like an Observable store does. It merely emit notifications when
the store content changes. Events are emitted for any of the following store operations:

* add
* put
* remove

A store event is a JavaScript key:value pairs object with the following ABNF
notation:

	store-event = "{" "type:" event-name "," "item:" object ["," "oldItem:" object] "}"
	event-name  = "new" / "change" / "delete"

To listen for store events the application has to register an event listener using either
the store's on() method or the dojo/on on() method. For example, if you want to get
notified of new objects being added to the store consider the following example:

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

Any store that exposes the cbtree/store/api/Store API can be made Eventable creating an
easy to use store notification system which carries alot less overhead than an observable
store does. (See the [observable](#observable) section below).

The cbtree/store/ObjectStore already has build-in event capabilities therefore wrapping
this type of store with Eventable has no effect. From a functional standpoint the following
two statements have the same effect:

	myStore = Eventable( new Hierarchy( ...) );
	myStore = new ObjectStore( ... )

However, the latter, offers better performance and carries less overhead.

<h2 id="observable">Observable</h2>

**dojo/store/Observable** is an object store wrapper that adds support for
notification of data changes to query result sets. The query result sets returned from
a Observable store will include a "observe" function that can be used to monitor the
query result for any changes due to changes in the store.

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

All cbtree stores can be made observable however, because the cbtree Object Store already
has event capabilities build-in it doesn't make sense to make it also observable. If you
need an observable store that support data hierarchy use the [Hierarchy](#the-hierarchy-store)
store instead of the Object store.

### IMPORTANT

Careful considerations should be made when selecting an observable store in a CheckBox Tree
environment. As the checked state of any tree node is maintained in the store, any update
to the object's checked state will result in running the updated object againts **ALL** observable
queries. If you have a large tree with many branches, keep in mind that each branch represents
a single store query which can severly impact the tree performance. In addition, if you use a
tree model with its _checkedStrict_ property set to true, each tree node represents a store
query.

**From a performance stand point the cbtree Object Store or an Eventable Store is the better choice.**

<h2 id="store-data-format">Store Data Format</h2>

By default, each store is loaded with plain JavaScript key:value pairs objects (hash)
which are either passed to the store constructor as the "data" keyword argument or by
means of a URL using the "url" keyword argument. Either way, the store expects the
data to be an array of plain objects.
The following is a valid object array:

```javascript
var myData = [
  { name:"Homer", lastName:"Simpson", hair:"none" },
  { name:"Marge", lastName:"Simpson", hair:"blue" },
  { name:"Bart" , lastName:"Simpson", hair:"blond" }
];

var myStore = new Memory( {data:myData} );
```

When loading data using a URL the data must be **JSON** encoded, that is, as of
dojo 1.8 all key or property names MUST be double-quoted strings. For the JSON
encoding rules please refer to [http://www.json.org](http://www.json.org/).

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



<h2 id="store-data-hierarchy">Store Data Hierarchy</h2>

In essence every `dojo/store` or derivative, including the `cbtree/store`, store
is, in most cases, a flat array of JavaScript objects without a natural order or
hierarchy. Lets take a look at a simple set of object as an example:

```javascript
[
  { name:"Audi"  ,type:"factory" },
  { name:"BMW"   ,type:"factory" },
  { name:"A3"    ,type:"sedan" },
  { name:"Q5"    ,type:"suv" },
  { name:"M3"    ,type:"sedan" },
  { name:"535"   ,type:"sedan" },
  { name:"750"   ,type:"sedan" }
]
```
The above objects all have a name and some type but there is no way we could
derive some sort of hierarchical relationship. To create a hierarchy we need
some object property, like *parent*, that defines the relation between the
objects. Both the cbtree [Hierarchy](#the-hierarchy-store) and [Object](#the-object-store) 
store provide support for a parent property which enable them to fetch either
the children or the parent(s) of an object. The actual object property to be
used is set using the store's **_parentProperty_** attribute. The default for
this property is "parent".

```javascript
[
  { name:"Audi"  ,type:"factory" },
  { name:"BMW"   ,type:"factory" },
  { name:"A3"    ,parent:"Audi", type:"sedan" },
  { name:"Q7"    ,parent:"Audi", type:"suv" },
  { name:"M3"    ,parent:"BMW" , type:"sedan" },
  { name:"535"   ,parent:"BMW" , type:"sedan" },
  { name:"750"   ,parent:"BMW" , type:"sedan" }
]
```
Above we have added a **_parent_** property to those objects who have an obvious
parent in our store. However, the objects 'Audi' and 'BMW' still don't have a 
*parent* property. Please note that instead of the default "parent" property
name we could have used a different property name like "manufaturer" and create
the store as follows:

```javascript
var myStore = new Hierarchy( {data:carData, idProperty:"name", parentProperty:"manufaturer", ...});
```
The updated set of objects now has a limited hierarchy, that is, not all objects
have a parent. In order to create a hierachy with a single root object, add one
more object that serves as the parent of 'Audi' and 'BMW'.

```javascript
[
  { name:"Cars"  ,parent:null  , type:"toys" },
  { name:"Audi"  ,parent:"Cars", type:"factory" },
  { name:"BMW"   ,parent:"Cars", type:"factory" },
  { name:"A3"    ,parent:"Audi", type:"sedan" },
  { name:"Q7"    ,parent:"Audi", type:"suv" },
  { name:"M3"    ,parent:"BMW" , type:"sedan" },
  { name:"535"   ,parent:"BMW" , type:"sedan" },
  { name:"750"   ,parent:"BMW" , type:"sedan" },
]
```
Because we now have an object store with a single root object we could select
a Tree Model with this store instead of a Forest Model. See the 
[Tree Store Model](Store-Models#tree-store-model) section and the 
[Hierarchy Store](Hierarchy-Store) for more details.

<h2 id="data-handlers">Data Handlers</h2>
If your data does not comply with the required [Store Data Format](#store-data-format)
the cbtree stores offer the option of so-called data handlers. All cbtree stores, 
with the exception of the FileStore, offer the option of pre-processing data, before
populating the store, using either one of the default **dojo/request** handlers
or registering a custom data handler.
Typically, a data handler takes an arbitrary data format and converts the data
returning an array of plain JavaScript key:value pairs objects ready for
consumption by the store loader.

This ability eliminates the need of having to create a new store for every data 
format or having to handle XHR requests yourself. In addition, because the stores
register any custom data handler with **dojo/request/handlers** the data handler
automatically becomes available to the **dojo/request** module.

The cbtree stores comes with two sample data handlers:

1. Comma Separated Value (CSV) data handler (csvHandler.js)
2. ItemFileReadStore data handler (ifrsHandler.js)

Both sample handlers can be found at **_/cbtree/store/handlers/_**

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
