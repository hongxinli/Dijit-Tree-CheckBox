The CheckBox Tree comes with three store models, a [Tree Store Model](#tree-store-model),
a [Forest Store Model](#forest-store-model) and a [File Store Model](#file-store-model).
The distinct differences are explained in the sections below. 

<h3>Content <span class="mega-icon mega-icon-readme"></span></h3>
* [The Model, an introduction](#the-model)
* [Tree Store Model](#tree-store-model)
* [Forest Store Model](#forest-store-model)
* [File Store Model](#file-store-model)
* [Selecting a Store](#selecting-a-store)
* [Model API Matrix](#model-api-matrix)

<h2 id="the-model">The Model</h2>
In a Model-View-Controller (MVC) pattern the model is the glue between the *View*
or presentation layer and something that manipulates the data (the controller).
Each controller may require a specific model. Please note that all models 
that come with the CheckBox Tree are so-called store models, that is,
they interface with objects (controllers) that expose the 
[cbtree/store/api/Store](Store-API) API.

All Checkbox Tree models implement the **cbtree/model/api/Model** API

### Model Foundation

The foundation of the CheckBox Tree models are the two base classes **_BaseStoreModel_**
and **_CheckedStoreModel_**. The class **_BaseStoreModel_** provides all 
the required logic to interface with a wide variety of store implementation. 
This class inspects a store and automatically extend it, if required and
possible, so that it complies with the minimum feature set required for the
models to operate. 

Please note that any model derived from the **_BaseStoreModel_** will also work
with the default `dijit/tree`. The base class **_CheckedStoreModel_** inherits 
for **_BaseStoreModel_** and adds
the capabilities to maintain and manipulate a so-called 'checked' state for store
objects.

The following table lists all [stores](Store#wiki-store-types) types and store
wrappers supported by the base class **_BaseStoreModel_**:

<table>
	<tr>
		<th>Provider</th>
		<th>Store Type</th>
		<th>Preferred</th>
		<th>Description</th>
	</tr>
	
	<tr>
		<td rowspan="6">CheckBox Tree</td>
		<td>cbtree/store/api/Store<sup>1</sup></td>
    <td></td>
		<td>
			Any store implenting the <strong>cbtree/store/api/Store</strong> API.
			The store must at a minimum implement the <em>get(), put()</em> and
			<em>query()</em> methods.
		</td>
	</tr>
	<tr>
		<td>Memory Store</td>
    <td></td>
		<td>
			A basic in memory store. The cbtree in memory store is similar to the
			dojo/store/Memory enhanced with additional capabilities.
		</td>
	</tr>
	<tr>
		<td>Hierarchy Store</td>
    <td><span class="mini-icon mini-icon-confirm"></span></td>
		<td>
			A derived from the Memory store adding support for natural order,
			hierarchical organized data object and multi-parenting.
		</td>	
	</tr>
	<tr>
		<td>Object Store</td>
		<td><span class="mini-icon mini-icon-confirm"></span></td>
		<td>Derived from the Hierarchy store with build in eventable capabilities.
	</tr>
	<tr>
		<td>File Store</td>
    <td><span class="mini-icon mini-icon-confirm"></span></td>
		<td>
			The File Store implements an in-memory store whose content represent the
			file system layout of the HTTP back-end server document root directory or
			portions thereof.
		</td>	
	</tr>
	<tr>
		<td>Eventable</td>
    <td></td>
		<td>
			A store wrapper that makes the store emit events for store updates.
		</td>	
	</tr>

	<tr>
		<td rowspan="2">Dojo Toolkt</td>
		<td>dojo/store/api/Store<sup>1</sup></td>
    <td></td>
		<td>
			Any store implenting the dojo/store/api/Store API. The store must at a
			minimum implement the <em>get(), put()</em> and <em>query()</em> methods.
		</td>
	</tr>
	<tr>
		<td>Observable</td>
    <td></td>
		<td>
			A store wrapper that enables the store query results to be made 'observable'.
		</td>
	</tr>
</table>

<span class="mega-icon mega-icon-exclamation"></span> All CheckBox Tree stores
are designed and implemented such that they can be wrapped with the dojo/store/Observable
store wrapper. Howerver, be sure to read the store [Eventable](Store#wiki-eventable) 
and [Observable](Store#wiki-observable) sections before doing so.

The folowing table lists the stores supported by CheckBox Tree model type.

<table>
  <tbody>
	<thead>
	  <tr>
	    <th style="width:150px;">Model</th>
	    <th>Dojo&nbsp;Store</th>
	    <th>CheckBox&nbsp;Tree&nbsp;Store</th>
	    <th>File&nbsp;Store</th>
	    <th>Observable</th>
	    <th>Eventable</th>
	  </tr>
	</thead>
    <tr style="vertical-align:top">
      <td>Tree Store Model</td>
      <td><span class="mini-icon mini-icon-confirm"></span></td>
      <td><span class="mini-icon mini-icon-confirm"></span></td>
      <td><span class="mini-icon mini-icon-x"></span></td>
      <td><span class="mini-icon mini-icon-confirm"></span></td>
      <td><span class="mini-icon mini-icon-confirm"></span></td>
    <tr style="vertical-align:top">
      <td>Forest Store Model</td>
      <td><span class="mini-icon mini-icon-confirm"></span></td>
      <td><span class="mini-icon mini-icon-confirm"></span></td>
      <td><span class="mini-icon mini-icon-x"></span></td>
      <td><span class="mini-icon mini-icon-confirm"></span></td>
      <td><span class="mini-icon mini-icon-confirm"></span></td>
    </tr>
    <tr style="vertical-align:top">
      <td>File Store Model</td>
      <td><span class="mini-icon mini-icon-x"></span></td>
      <td><span class="mini-icon mini-icon-x"></span></td>
      <td><span class="mini-icon mini-icon-confirm"></span></td>
      <td><span class="mini-icon mini-icon-x"></span></td>
      <td><span class="mini-icon mini-icon-confirm"></span></td>
    </tr>
  </tbody>
</table>

<h3 id="store-requirements">Store Requirements</h3>
In order for any store to work with the CheckBox Tree store models they must,
at a minimum, implement the following `cbtree/store/api/Store` API functions:

* [get()](Store-API#wiki-get)
* [put()](Store-API#wiki-put)
* [query()<sup>1</sup>](Store-API#wiki-query)

<sup>1</sup> In case of a Forest Store Model the store must implement either
the [query()](Store-API#wiki-query) or [getChildren()](Store-API#wiki-getchildren)
function.

<h3 id="extending-a-store">Extending a Store</h3>
The **_BaseStoreModel_** class will automatically try to extend a store if
certain functionality is not offered by the store. The model tests the store for
the presence of the [hierarchical](Store-API#wiki-hierarchical) property.
If missing, or set to false, the model adds *before* advice to the store
methods `add()` and `put()` adding support for the [PutDirectives](Store-API#wiki-putdirectives)
property **_parent_**. 

If the store does not have a [getChildren()](Store-API#wiki-getchildren)
method one will be added using the store's [query()](Store-API#wiki-query)
function. If the store does not have a `query()` method either an exception
is thrown.

If the store has a [load()](Store-API#wiki-load) method it is called just before
the first store query is executed.

<h3 id="creating-a-model">Creating a Model</h3>

In order to explain some of the models inner workings we need to declare a simple
data set which we will refer back to throughout this document. Lets assume we 
have the following set of objects:

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
```

When instantiating a model the user must provide a store instance and optionally
a tree root query. This query is passed to the store when a tree issues a
*getRoot()* request. The object returned as the query result will then serve as
the root of our tree assuming the store query returns a single object.

```javascript
required(["cbtree/store/Hierarchy"           // Fetch the store, 
          "cbtree/model/TreeStoreModel",     // the model,
          "cbtree/Tree"                      // and the Tree
         ], function (Hierarchy, TreeStoreModel, Tree) {
                  ...
  // Create the store and load it with our data set.
  var myStore = new Hierarchy( { data:myData } );
  var myModel = new TreeStoreModel({ store:myStore, query:{type:"root"} });
  var myTree  = new Tree( { model:myModel }, "some-html-div-id" );
  myTree.startup();
});
```
The above example shows the basic sequence required to create a store, model 
and tree. The following sections will explain in detail which model to use.

Which store model to use primarily depends on:

1. How many store objects matched the root query.







<h2 id="tree-store-model">Tree Store Model</h2>

The Tree Store Model is used if, and only if, the tree root query is guaranteed
to return a single data store object. The store data structure itself is of no
importance. For example, the following example will use the sample [reference
data](#creating-a-model) listed above:

```javascript
var store = new Hierarchy( {data:myData} );
var model = new TreeStoreModel( {store: store, query: {type:"root"}} );
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






<h2 id="forest-store-model">Forest Store Model</h2>

The Forest Store Model is used whenever the tree root query could potentially
return multiple store objects. The Forest Store model fabricates a artificial
root object and the objects returned as the result of the root query become
children of this fabricate root. The fabricated root object does **NOT** represent
any store object and is merely used to anchor the tree.

There are two specific properties that effect the fabricated root, the model
property [checkedRoot](Model-API#wiki-checkedroot) and the tree property [showRoot](CheckBox-Tree-API#wiki-showroot). 
If the tree property **_showRoot_** is set to false the fabricated root is not 
displayed as part of the tree.

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

If you can structure your data and associated root query such that a single store
object is returned it is recommended to do so and use the Tree Store Model 
instead because of the additional overhead of a Forest Tree Store.


<h2 id="file-store-model">File Store  Model</h2>

The File Store Model allows the user to present the back-end server file system
as a traditional UI directory tree. 
The model is designed to be used with the cbtree FileStore which, like the other
cbtree stores, implements the `cbtree/store/api/Store` API offering the functionality
to query the back-end servers file system, add lazy loading and provide limited
support for store write operations.
Please refer to the [File Store](File-Store) documentation for details and 
examples. 

Because the content of a File Store is treated as read-only, that is, you can't
add new items to the store, any attempt to do so will throw an exception. You can 
however add custom properties to store items which will be writeable, or rename
or delete store items. The File Store Model also supports drag and drop
operations using the File Store rename capabilities.

<span class="mega-icon mega-icon-exclamation"></span> Because of the reduced
function set supported, some of the common store model properties are ignored 
by the File Store Model.






<h2 id="selecting-a-store">Selecting a Store</h2>
Although the cbtree models can operate on a wide variety of stores it is important to
understand the pros and cons of each of them. This section gives general guidelines
for the best store practices...


<h2 id="model-api-matrix">Model API Matrix</h2>

The following tables show which `cbtree/model/api/Model` API [properties](Model-API#model-properties)
and [functions](Model-API#model-functions) each model implements.

<table>
	<tr>
		<th>Model API</th>
		<th>Name</th>
		<th>Tree Store Model</th>
		<th>Forest Store Model</th>
		<th>File Store Model</th>
	</tr>
	<tr>
		<td rowspan="15">Property</td>
		<td><a href="Model-API#wiki-checkedall">checkedAll</a></td>
		<td><span class="mini-icon mini-icon-confirm"></span></td>
		<td><span class="mini-icon mini-icon-confirm"></span></td>
		<td><span class="mini-icon mini-icon-confirm"></span></td>
	</tr>
	<tr>
		<td><a href="Model-API#wiki-checkedattr">checkedAttr</a></td>
		<td><span class="mini-icon mini-icon-confirm"></span></td>
		<td><span class="mini-icon mini-icon-confirm"></span></td>
		<td><span class="mini-icon mini-icon-confirm"></span></td>
	</tr>
	<tr>
		<td><a href="Model-API#wiki-checkedstate">checkedState</a></td>
		<td><span class="mini-icon mini-icon-confirm"></span></td>
		<td><span class="mini-icon mini-icon-confirm"></span></td>
		<td><span class="mini-icon mini-icon-confirm"></span></td>
	</tr>
	<tr>
		<td><a href="Model-API#wiki-checkedroot">checkedRoot</a></td>
		<td><span class="mini-icon mini-icon-confirm"></span></td>
		<td><span class="mini-icon mini-icon-confirm"></span></td>
		<td><span class="mini-icon mini-icon-confirm"></span></td>
	</tr>
	<tr>
		<td><a href="Model-API#wiki-checkedstrict">checkedStrict</a></td>
		<td><span class="mini-icon mini-icon-confirm"></span></td>
		<td><span class="mini-icon mini-icon-confirm"></span></td>
		<td><span class="mini-icon mini-icon-confirm"></span></td>
	</tr>
	<tr>
		<td><a href="Model-API#wiki-">enabledAttr</a></td>
		<td><span class="mini-icon mini-icon-confirm"></span></td>
		<td><span class="mini-icon mini-icon-confirm"></span></td>
		<td><span class="mini-icon mini-icon-confirm"></span></td>
	</tr>
	<tr>
		<td><a href="Model-API#wiki-iconattr">iconAttr</a></td>
		<td><span class="mini-icon mini-icon-confirm"></span></td>
		<td><span class="mini-icon mini-icon-confirm"></span></td>
		<td><span class="mini-icon mini-icon-confirm"></span></td>
	</tr>
	<tr>
		<td><a href="Model-API#wiki-labelattr">labelAttr</a></td>
		<td><span class="mini-icon mini-icon-confirm"></span></td>
		<td><span class="mini-icon mini-icon-confirm"></span></td>
		<td><span class="mini-icon mini-icon-confirm"></span></td>
	</tr>
	<tr>
		<td><a href="Model-API#wiki-multistate">multiState</a></td>
		<td><span class="mini-icon mini-icon-confirm"></span></td>
		<td><span class="mini-icon mini-icon-confirm"></span></td>
		<td><span class="mini-icon mini-icon-confirm"></span></td>
	</tr>
	<tr>
		<td><a href="Model-API#wiki-normalize">normalize</a></td>
		<td><span class="mini-icon mini-icon-confirm"></span></td>
		<td><span class="mini-icon mini-icon-confirm"></span></td>
		<td><span class="mini-icon mini-icon-confirm"></span></td>
	</tr>
	<tr>
		<td><a href="Model-API#wiki-parentproperty">parentProperty</a></td>
		<td><span class="mini-icon mini-icon-confirm"></span></td>
		<td><span class="mini-icon mini-icon-confirm"></span></td>
		<td></span></td>
	</tr>
	<tr>
		<td><a href="Model-API#wiki-query">query</a></td>
		<td><span class="mini-icon mini-icon-confirm"></span></td>
		<td><span class="mini-icon mini-icon-confirm"></span></td>
		<td><span class="mini-icon mini-icon-confirm"></span></td>
	</tr>
	<tr>
		<td><a href="Model-API#wiki-rootlabel">rootLabel</a></td>
		<td><span class="mini-icon mini-icon-confirm"></span></td>
		<td><span class="mini-icon mini-icon-confirm"></span></td>
		<td><span class="mini-icon mini-icon-confirm"></span></td>
	</tr>
	<tr>
		<td><a href="Model-API#wiki-rootid">rootId</a></td>
		<td></td>
		<td><span class="mini-icon mini-icon-confirm"></span></td>
		<td></td>
	</tr>
	<tr>
		<td><a href="Model-API#wiki-store">store</a></td>
		<td><span class="mini-icon mini-icon-confirm"></span></td>
		<td><span class="mini-icon mini-icon-confirm"></span></td>
		<td><span class="mini-icon mini-icon-confirm"></span></td>
	</tr>
</table>


<table>
	<tr>
		<th>Model API</th>
		<th>Name</th>
		<th>Tree Store Model</th>
		<th>Forest Store Model</th>
		<th>File Store Model</th>
	</tr>
	<tr>
		<td rowspan="12">Function</td>
		<td><a href="Model-API#wiki-getchecked">getChecked</a></td>
		<td><span class="mini-icon mini-icon-confirm"></span></td>
		<td><span class="mini-icon mini-icon-confirm"></span></td>
		<td><span class="mini-icon mini-icon-confirm"></span></td>
	</tr>
	<tr>
		<td><a href="Model-API#wiki-getchildren">getChildren</a></td>
		<td><span class="mini-icon mini-icon-confirm"></span></td>
		<td><span class="mini-icon mini-icon-confirm"></span></td>
		<td><span class="mini-icon mini-icon-confirm"></span></td>
	</tr>
	<tr>
		<td><a href="Model-API#wiki-getenabled">getEnabled</a></td>
		<td><span class="mini-icon mini-icon-confirm"></span></td>
		<td><span class="mini-icon mini-icon-confirm"></span></td>
		<td><span class="mini-icon mini-icon-confirm"></span></td>
	</tr>
	<tr>
		<td><a href="Model-API#wiki-geticon">getIcon</a></td>
		<td><span class="mini-icon mini-icon-confirm"></span></td>
		<td><span class="mini-icon mini-icon-confirm"></span></td>
		<td><span class="mini-icon mini-icon-confirm"></span></td>
	</tr>
	<tr>
		<td><a href="Model-API#wiki-getidentity">getIdentity</a></td>
		<td><span class="mini-icon mini-icon-confirm"></span></td>
		<td><span class="mini-icon mini-icon-confirm"></span></td>
		<td><span class="mini-icon mini-icon-confirm"></span></td>
	</tr>
	<tr>
		<td><a href="Model-API#wiki-getlabel">getLabel</a></td>
		<td><span class="mini-icon mini-icon-confirm"></span></td>
		<td><span class="mini-icon mini-icon-confirm"></span></td>
		<td><span class="mini-icon mini-icon-confirm"></span></td>
	</tr>
	<tr>
		<td><a href="Model-API#wiki-getparents">getParents</a></td>
		<td><span class="mini-icon mini-icon-confirm"></span></td>
		<td><span class="mini-icon mini-icon-confirm"></span></td>
		<td><span class="mini-icon mini-icon-confirm"></span></td>
	</tr>
	<tr>
		<td><a href="Model-API#wiki-getroot">getRoot</a></td>
		<td><span class="mini-icon mini-icon-confirm"></span></td>
		<td><span class="mini-icon mini-icon-confirm"></span></td>
		<td><span class="mini-icon mini-icon-confirm"></span></td>
	</tr>
	<tr>
		<td><a href="Model-API#wiki-isitem">isItem</a></td>
		<td><span class="mini-icon mini-icon-confirm"></span></td>
		<td><span class="mini-icon mini-icon-confirm"></span></td>
		<td><span class="mini-icon mini-icon-confirm"></span></td>
	</tr>
	<tr>
		<td><a href="Model-API#wiki-mayhavechildren">mayHaveChildren</a></td>
		<td><span class="mini-icon mini-icon-confirm"></span></td>
		<td><span class="mini-icon mini-icon-confirm"></span></td>
		<td><span class="mini-icon mini-icon-confirm"></span></td>
	</tr>
	<tr>
		<td><a href="Model-API#wiki-setchecked">setChecked</a></td>
		<td><span class="mini-icon mini-icon-confirm"></span></td>
		<td><span class="mini-icon mini-icon-confirm"></span></td>
		<td><span class="mini-icon mini-icon-confirm"></span></td>
	</tr>
	<tr>
		<td><a href="Model-API#wiki-setenabled">setEnabled</a></td>
		<td><span class="mini-icon mini-icon-confirm"></span></td>
		<td><span class="mini-icon mini-icon-confirm"></span></td>
		<td><span class="mini-icon mini-icon-confirm"></span></td>
	</tr>

</table>

