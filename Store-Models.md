The CheckBox Tree comes with three store models, a [Tree Store Model](#tree-store-model),
a [Forest Store Model](#forest-store-model) and a [File Store Model](#file-store-model).
The distinct differences are explained in the sections below. Which store
model to use primarily depends on:

1. How you are going to query the store.

Lets assume we have the following set of objects loaded in a 
[Hierarchy](Hierarchy-Store) store:

```javascript
var myData = [
  { name:"Family"    , type:"root"                                           },
  { name:"Abe"       , type:"parent", parent:["root"]         , hair:"Brown" },
  { name:"Jacqueline", type:"parent", parent:["root"]         , hair:"Brown" },
  { name:"Homer"     , type:"parent", parent:["Abe"]          , hair:"none"  },
  { name:"Marge"     , type:"parent", parent:["Jacqueline"]   , hair:"blue"  },
  { name:"Bart"      , type:"child" , parent:["Homer","Marge"], hair:"blond" },
  { name:"Lisa"      , type:"child" , parent:["Homer","Marge"], hair:"blond" },
  { name:"Maggie"    , type:"child" , parent:["Homer","Marge"], hair:"blond" }
];

var myStore = new Hierarchy( {data:myData} );
```
This data set has a prefect hierarchy with a single root object named "Family".
Notice that when creating the store we do **NOT** set the store's *idProperty* 
property, this because we could have duplicate names in our family tree. As a 
result the cbtree store will automatically add an additional property called
"id" to each object and assign it a unique numeric key.

<h2 id="tree-store-model">Tree Store Model</h2>

The Tree Store Model is used if, and only if, the store query is guaranteed
to return a single data store object. The store data structure itself is of no
importance. For example, the following example will use the sample reference
store listed above:

```javascript
var store = new Hierarchy( {data:myData} );
var model = new TreeStoreModel( {store: store, query: {type:'root'}} );
```
In this example we known the query is guaranteed to return a single store object
with the name 'Family' therefore we can safely use the Tree Store Model located
at **_cbtree/model/TreeStoreModel_**.

However, querying the same store with a query argument like `query:{type:"parent"}`
would throw an exception because this particular query would return 4 store 
objects instead of just 1.
This shows that even though the store data is needly organized in a tree hierarchy 
it all depends on the number of objects the root query returns if we can use a
Tree Store Model. 

If you can structure your data and associated root query such that a single store
object is returned it is recommended to do so because of the added overhead of
a Forest Tree Store.



<h2 id="forest-store-model">Forest Store Model</h2>

The Forest Store Model is used whenever a store query could potentially return
multiple store items in which case the model will fabricate a artificial root
item. The fabricated root item does **NOT** represent any store object and is
merely used to anchor the tree.

```javascript
var store = new Hierarchy( {data:myData} );
var model = new ForestStoreModel( {store: store, query: {name:/.*/}} );

                      or simply 
                      
var model = new ForestStoreModel( {store: store} );
```

In the example above the query returns all available store objects. Notice that
a regular expression was used to query the object property *name*. See the 
[Query Engine](Query-Engine) for a detailed description on how to query a store.  

The next example returns 4 store objects
```javascript
var store = new Hierarchy( {data:myData} );
var model = new ForestStoreModel( {store: store, query: {type:"parent"}} );
```

<h2 id="tree-versus-forest-model">Tree versus Forest Model</h2>


<h2 id="file-store-model">File Store  Model</h2>

<h2 id="store-model-properties">Store Model Properties</h2>

<h2 id="store-model-api">Store Model API</h2>

<h2 id="wiki-selecting-a-store">Selecting a Store</h2>
Although the cbtree models can operate on a wide variety of stores it is important to
understand the pros and cons of each of them. This section gives general guidelines
for the best store practices...