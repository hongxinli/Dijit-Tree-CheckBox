The CheckBox Tree comes with three store models, a [Tree Store Model](#tree-store-model),
a [Forest Store Model](#forest-store-model) and a [File Store Model](#file-store-model).
The distinct differences are explained in the sections below. 

<h3>Content <span class="mega-icon mega-icon-readme"></span></h3>
* [The Model, an introduction](#the-model)
* [Tree Store Model](#tree-store-model)
* [Forest Store Model](#forest-store-model)
* [File Store Model](#file-store-model)
* [Store Model Properties](#store-model-properties)
* [Store Model API](#store-model-api)
* [Selecting a Store](#selecting-a-store)

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
		<td>cbtree/store/api/Store</td>
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
		<td>dojo/store/api/Store</td>
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
property **_checkedRoot_** and the tree property [showRoot](CheckBox-Tree#showRoot). 
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



<h2 id="store-model-properties">Store Model Properties</h2>

Model properties define specific features or characteristics of a store model,
The property names also represent properties in the keyword object passed to 
the model constructor. For example:

```javascript
require(["cbtree/model/TreeStoreModel", ... ], function (TreeStoreModel, ... ) {
  var keywordArgs = { store:myStore, checkedAll:true, query:{type:"Monarch"} };
  var model = new TreeStoreModel( keywordArgs );
}
```

#### checkedAll: 
> **_TYPE_**: Boolean

> If true, every store object will receive a so-called 'checked' state property.
> The name of the actual property is defined by the value of the model property 
> **_checkedAttr_**. If the 'checked' state property is added to a store object,
> its initial state is defined by the value of the model property **_checkedState_**
> (see also *checkedAttr*)

> **_DEFAULT_**: true

#### checkedAttr: 
> **_TYPE_**: String

> The property name of a store object that holds its *checked* state. On store 
> load it specifies the object's initial checked state.
> For example: `{ name:"Egypt", type:"country", checked: true }`
> If a store object has no *checked* property it depends on the model property 
> **_checkedAll_** if one will be added in which case the initial value is 
> set according to the value of **_checkedState_**.

> **_DEFAULT_**: "checked"

<span class="mega-icon mega-icon-exclamation"></span> If a store object has no
'checked' state property and **_checkedAll_** is *false* no checkbox will be 
created by the tree for the object.

#### checkedState: 
> **_TYPE_**: Boolean

> The default *checked* state applied to every store item unless otherwise
> specified in the object store (see also: *checkedAttr*)

> **_DEFAULT_**: false

#### checkedRoot: 
> **_TYPE_**: Boolean

> If true, the tree root node will receive a checked state even though it may not 
> represent an actual entry in the store. This property is independent of the
> tree property **_showRoot_**. If the tree property *showRoot* is set to false the
> checked state for the root will not show either.

> **_DEFAULT_**: false

#### checkedStrict: 
> **_TYPE_**: Boolean | String

> If true, a strict parent-child relation is maintained. For example, if all 
> children of an object are checked the parent will automatically receive the
> same checked state. If any of the children are unchecked the parent will, 
> depending on, if multi state is enabled, receive either a mixed or unchecked
> state. If set to "inherit", children will inherit the parent checked state
> when changed. However, the parent checked state is **_NOT_** updated when a
> child's checked state changes.

> **_DEFAULT_**: true

<span class="mega-icon mega-icon-exclamation"></span> If set to true, the model
will request a full store load which may impact performance on very large object
stores like a File Store.

#### enabledAttr: 
> **_TYPE_**: String

> The name of the store object property that holds the 'enabled' state of the
> checkbox or alternative widget. Note: Eventhough it is referred to as the
> 'enabled' state the tree will only use this property to enable/disable the 
> 'ReadOnly' property of a checkbox or alternative widget. This because disabling
> a widget (DOM element) may exclude it from HTTP POST operations.

> **_DEFAULT_**: null

#### iconAttr: 
> **_TYPE_**: String

> The name of the store object property whose value is considered a tree node
> icon (css class name). If specified, get the icon from an object using this
> property name. (see [Tree Styling](Tree-Styling))

> **_DEFAULT_**: ""

#### labelAttr: 
> **_TYPE_**: String

> The name of the store object property whose value is returned as the label
> for the tree node.

> **_DEFAULT_**: "name"

#### multiState: 
> **_TYPE_**: Boolean

> Determines if the object checked state is to be maintained as multi state or 
> as dual state, that is, {"mixed", true, false} versus {true, false}. If true
> multi state is enabled.

> **_DEFAULT_**: true

#### normalize: 
> **_TYPE_**: Boolean

> If true, the checked state of any non branch (leaf) checkbox is normalized, 
> that is, true or false. When normalization is enabled checkboxes associated
> with tree leafs (e.g. nodes without children) can never have a "mixed" state.

> **_DEFAULT_**: true

#### parentProperty:
> **_TYPE_**: String

> The property name of a store object whose value represents the object's parent
> id or ids. If the store assigned to the model has a **_parentProperty_** 
> property the store value is used.

> **_DEFAULT_**: "parent"

#### query: 
> **_TYPE_**: Object

> A JavaScript key:value pairs object used the query the store to determine the
> tree root object or, in case the query returns multiple objects, the children
> of the fabricated root object. For example: `{type:"parent"}`.
> If not specified, a wildcard search is performed using the store's 
> **_idProperty_** property value. (See also [Tree Store Model](#tree-store-model)
> and [Forest Store Model](#forest-store-model))

> **_DEFAULT_**: null

#### rootLabel: 
> **_TYPE_**: String

> Label of the fabricated tree root object ([Forest Store Model](#forest-store-model)
> only) or the alternative label in case the tree root is represented by a store
> object ([Tree Store Model](#tree-store-model) only)

> **_DEFAULT_**: "ROOT"

#### rootId: 
> **_TYPE_**: String

> ID of the fabricated root item, Only valid for a Forest Store Model.

> **_DEFAULT_**: "$root$"

#### store: 
> **_TYPE_**: Object

> The underlying object store.

> **_DEFAULT_**: null





<h2 id="store-model-api">Store Model API</h2>

The following section list the default functions available with the CheckBox Tree
store models.

### fetchItemByIdentity( keywordArgs )
> Given the identity of an item, this method returns the item that has that 
> identity through the onItem callback.

**_keywordArgs:_** Object
> A JavaScript key:value pairs object that defines the object to locate and
> callbacks to invoke when the object has been located. The format of the object
> is as follows:
>
<pre>
{
  identity: String|Number,
  onItem: Function,
  onError: Function,
  scope: object
}
</pre>

**returns:** void

<span class="mega-icon mega-icon-exclamation"></span> This method is for backward
compatability with **dijit/tree/model** only. use `store.get(identity)` instead.

*********************************************
### getChecked ( item )
> Get the current checked state from the object store. The checked state
> in the store can be: 'mixed', true, false or undefined. Undefined in this
> context means the object has no checked property (see checkedAttr).

**_item:_** Object
> A valid store object.

**returns:** Boolean | String | undefined

*********************************************
#### getChildren( parent, onComplete, onError )
> Calls onComplete() with an array of child items of given parent item.
> Note: Only the immediate descendents are returned.

**_parent:_** Object
> A valid store object.

**_onComplete:_** Function (optional)
> If an onComplete callback is specified, the callback function will be called
> just once, as *onComplete( children )*.

**_onError:_** Function (optional)
> Called when an error occured.

**returns:** void

*********************************************
#### getEnabled( item )
> Returns the current 'enabled' state of an item as a boolean. See the *enabledAttr*
> property description for more details.

**_item:_** Object
> A valid store object.

**returns:** Boolean
> The state returned is the value of the checked widget "readOnly" property.

*********************************************
#### getIcon( item )
> If the *iconAttr* property of the model is set, get the icon for *item* from
> the store otherwise *undefined* is returned.

**_item:_** Object
> A valid store object.

**returns:** Object | String
> Returns an [icon](Tree-Styling#wiki-icon-properties) object or a string


*********************************************
#### getIdentity( item )
> Get the identity of an item.

**_item:_** Object
> A valid store object.

**returns:** String | Number

*********************************************
#### getLabel( item )
> Get the label for an item. If the model property *labelAttr* is set, the
> associated property of the item is retrieved otherwise the *labelAttr*
> property of the store is used.

**_item:_** Object
> A valid store object.

**returns:** String

*********************************************
#### getParents ( item )
> Get the parent(s) of a store item. Returns an array of store items.	

**_item:_** Object
> A valid store object.

**returns:** dojo/promise/Promise
> Returns a promise. If resolved the result of the promise is an array of parent
> objects.

*********************************************
#### getRoot( onItem, onError )
> Calls onItem with the root item for the tree, possibly a fabricated item.
> Calls onError on error.

**_onItem:_** Function
> Callback function. On successful completion called as *onItem( root )*.

**_onError:_** Function (optional)
> Called when an error occured.

**returns:** void

*********************************************
#### isItem( something )
> Returns true if *something* is an item and came from this model instance.
> Returns false if *something* is a literal, an item from another model instance,
> or is any object other than an item.

**_something:_** Any

**returns:** Boolean

*********************************************
#### mayHaveChildren( item )
> Returns true if an item has or may have children.

**_item:_** Object
> A valid store object.

**returns:** Boolean

*********************************************
#### setChecked ( item, newState )
> Update the checked state of a store item. If the model property *checkedStrict*
> is true, the items parent(s) and children, if any, are updated accordingly.

**_item:_** Object
> A valid store object.

**_newState:_** Boolean | String
> The new checked state. The state can be either a boolean (true | false) or a string ('mixed')

**returns:** void

*********************************************
#### setEnabled( item, value )
> Set the new 'enabled' state of an item. See the *enabledAttr* property description for more
> details.

**_item:_** Object
> A valid store object.

**_value:_** Any

**returns:** void









<h2 id="selecting-a-store">Selecting a Store</h2>
Although the cbtree models can operate on a wide variety of stores it is important to
understand the pros and cons of each of them. This section gives general guidelines
for the best store practices...
