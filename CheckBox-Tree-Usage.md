
<h3>Content <span class="mega-octicon octicon-book"></span></h3>
* [Working with Events](#working-with-events)
* [Deleting Tree Node](#deleting-tree-nodes)
* [Sorting Tree Node](#sorting-tree-nodes)

<h2 id="working-with-events">Working with Events</h2>

With dojo 1.7 a new event handling module was introduced called `dojo/on`.
The intend of this module is/was to make event handling with dojo easier and
mimic some of the DOM4 Event system. However, `dojo/on` is not a self contained
DOM-4 Event system implementation.
In addition, dijit widgets inherit a modified version of `dojo/on` to make it
backward compatible with callback methods and non-DOM compliant events.
The `dijit/_WidgetBase.on()` method tries to map event types, case insensitive
that is, to callback functions whose name starts with "on" like *onClick()* or
*onDblClick()*.

The next sections discuss the difference in implementation of event listeners
for callbacks and events.

<h3><span class="mega-octicon octicon-triangle-right"></span>Callbacks</h3>

Before registering an event listener with a dijit widget we must first check if
the widget has a method that could be auto linked with the event type.
For example, if we want to listen for an event named "myEvent", does the widget
have a method called `onMyEvent()` or `onmyevent()`?
If so, we need to define our event listener as a callback function with the
same set of argument as the widget's onMyEvent() method.

Let's assume our widget has a method (callback) called `onMyEvent()` with a
signature like: `onMyEvent( item, node, event )` in which case we can establish
our event listener in two ways:

1. Using `dojo/aspect` or,
2. Call the widget's `on()` method.

##### Using dojo/aspect.
Using `dojo/aspect` we attach **_after_** advice to the widget method so our event
listener gets called each time the widget calls `onMyEvent()` like:

```javascript
require(["dojo/aspect", "cbtree/Tree", ... ], function (aspect, Tree, ... ) {
    // Event listener
    function clickEvent( item, node, event ) {
      console.log( "A Tree Node was clicked" );
    }
                   ...
    var myTree = new Tree( {model:someModel, ... } );
    aspect.after( myTree, "onMyEvent", clickEvent, true );
                   ...
})
```
<span class="mega-octicon octicon-alert"></span> Make sure the fourth
argument is set to **_true_** otherwise `clickEvent()` is not called with the
original arguments of `onMyEvent()` but with the result instead. Also, notice
that when calling `aspect.after()` we specify the callback function name
"onMyEvent" and not an event type.



##### Using widget on() method.
Using the widget's `on()` method we specify the event type "myEvent" and not the
callback function name, like:

```javascript
require(["cbtree/Tree", ... ], function (Tree, ... ) {
    // Event listener
    function clickEvent( item, node, event ) {
      console.log( "A Tree Node was clicked" );
    }
                   ...
    var myTree = new Tree( {model:someModel, ... } );
    myTree.on( "myEvent", clickEvent );
                   ...
})
```
The above example accomplishes exactly the same thing as dojo/aspect did in the
first example simply because the underlaying `_WidgetBase.on()` method is able
to map the event type "myEvent" to the callback `onMyEvent()` and as a result
calls dojo/aspect on our behalf.

<h3><span class="mega-octicon octicon-triangle-right"></span>Events</h3>

This time, let's assume our widget does **_NOT_** have a callback that could be
auto linked to the event type "myEvent". Instead the widget actually emits events
of type "myEvent". In this case both dojo/aspect and the widget's on() method
will automatically create the callback, which in our case will be named "onmyEvent".

The big difference between the widget calling callback functions programmatically
and emitting events is the way the event listener will be called. In the latter
case the event handlers are called with just one argument, the event.

```javascript
require(["cbtree/Tree", ... ], function (Tree, ... ) {
    // Event listener
    function clickEvent( event ) {
      console.log( "A Tree Node was clicked" );
    }
                   ...
    var myTree  = new Tree( {model:someModel, ... } );
    myTree.on( "myEvent", clickEvent );
                   ...
})
```
In the above example notice that the function `clickEvent()` now is called with
just a single argument **_event_**. The event is a JavaScript key:value pairs
object.

<span class="mega-octicon octicon-alert"></span> In case of true events
that is, the event name can not be mapped to a callback function, the event name
is handled case sensitive. Therefore, as a good practise, always use the correct
case for event names regardless if you are registering for an event or callback.
Note that DOM-3/4 always handles event types case sensitive.

<h2 id="callbacks-and-events">Callbacks and Events</h2>

The CheckBox Tree modules use both callbacks and events. Callbacks are used
whenever dojo or dijit API's require them, for example the dijit model API
`dijit/tree/model`, otherwise events are used.

The tables below list the event names and associated callback by CheckBox Tree
module. If for any given event type a callback is specified, the arguments
column specifies the list of arguments passed to the event listener.

The arguments passed to event handles are defined as follows:

<table>
	<tr>
		<th>Argument</th>
		<th>Description</th>
	</tr>
	<tr>
		<td>event</td>
		<td>Event object, the object is context specific</td>
	</tr>
	<tr>
		<td>item</td>
		<td>Store object</td>
	</tr>
	<tr>
		<td>node</td>
		<td>Tree node widget</td>
	</tr>
</table>

### Tree Events
<table>
	<tr>
		<th>Event Type</th>
		<th>Callback</th>
		<th>Arguments</th>
		<th>Description</th>
	</tr>
	<tr>
		<td>click</td>
    <td>onClick</td>
    <td>(item, node, event)</td>
		<td>
			A tree node is clicked.
		</td>
	</tr>
	<tr>
		<td>checkBoxClick</td>
    <td>onCheckBoxClick</td>
    <td>(item, node, event)</td>
		<td>
			A checkbox is clicked.
		</td>
	</tr>
	<tr>
		<td>close</td>
    <td>onClose</td>
    <td>(item, node)</td>
		<td>
			Node closed, a tree node collapsed.
		</td>
	</tr>
	<tr>
		<td>dblClick</td>
    <td>onDblClick</td>
    <td>(item, node, event)</td>
		<td>
			Double click
		</td>
	</tr>
	<tr>
		<td>open</td>
    <td>onOpen</td>
    <td>(item, node)</td>
		<td>
			Node opened. a tree node is expanded.
		</td>
	</tr>
	<tr>
		<td>event</td>
    <td>onEvent</td>
    <td>(item, event, value)</td>
		<td>
			User event successful.
		</td>
	</tr>
	<tr>
		<td>load</td>
    <td>onLoad</td>
    <td>(void)</td>
		<td>
			Tree finished loading.
		</td>
	</tr>
</table>




### Model Events
The CheckBox Tree models, although not widgets, offer the same `on()`
functionality as widgets do. Therefore you can simply register a model event
listener like `model.on( "dataValidated", myEventHandler );`

<table>
	<tr>
		<th>Event Type</th>
		<th>Callback</th>
		<th>Arguments</th>
		<th>Description</th>
	</tr>
	<tr>
		<td>change</td>
    <td>onChange</td>
    <td>(item, propertyName, newValue, oldValue)</td>
		<td>
			Property of a store item changed.
		</td>
	</tr>
	<tr>
		<td>childrenChange</td>
    <td>onChildrenChange</td>
    <td>(parent, newChildrenList)</td>
		<td>
			The children of a node changed.
		</td>
	</tr>
	<tr>
		<td>dataValidated</td>
    <td>onDataValidated</td>
    <td>(void)</td>
		<td>
			The store data has been validated.
		</td>
	</tr>
	<tr>
		<td>delete</td>
    <td>onDelete</td>
    <td>(item)</td>
		<td>
			An item was removed from the store.
		</td>
	</tr>
	<tr>
		<td>pasteItem</td>
    <td>onPasteItem</td>
    <td>(item, insertIndex, before)</td>
		<td>
			Item was pasted at a new location.
		</td>
	</tr>
	<tr>
		<td>rootChange</td>
    <td>onRootChange</td>
    <td>(item, action)</td>
		<td>
			The children of the tree root changed.
		</td>
	</tr>
	<tr>
		<td>reset</td>
    <td>onReset</td>
    <td>(void)</td>
		<td>
			The model is being reset.
		</td>
	</tr>
</table>

### Store Events

<span class="mega-octicon octicon-alert"></span> Store events are available
if, and only if, the store is made [Eventable](Store#wiki-eventable) or the
store is a [cbtree/store/Object](Store#wiki-the-object-store) store.

<table>
	<tr>
		<th>Event Type</th>
		<th>Callback</th>
		<th>Argument</th>
		<th>Description</th>
	</tr>
	<tr>
		<td>change</td>
    <td></td>
    <td>(event)</td>
		<td>
			Property of a store object changed.
		</td>
	</tr>
	<tr>
		<td>close<sup>1</sup></td>
    <td>onClose</td>
    <td>(count, cleared)</td>
		<td>
			The store was closed.
		</td>
	</tr>
	<tr>
		<td>new</td>
    <td></td>
    <td>(event)</td>
		<td>
			A new store object was added.
		</td>
	</tr>
	<tr>
		<td>remove</td>
    <td></td>
    <td>(event)</td>
		<td>
			A store object was removed/deleted.
		</td>
	</tr>
</table>
<sup>1</sup> The close event is the only store event using a callback and therefore
accessible even if the store isn't made eventable.

### An example
The following example demonstrate the use of events and callbacks:

```javascript
require( ["cbtree/Tree",
          "cbtree/model/ForestStoreModel",
          "cbtree/store/Memory",
          "cbtree/store/Eventable"], function (Tree, ForestStoreModel, Memory, Eventable) {
                    ...
  // Create an eventable store and listen for events of type "new".
  var myStore = Eventable( new Memory( {data: myData, idProperty:"name"} ));
  myStore.on( "new", function (event) {
    console.log( "An event of type: " + event.type + "was recieved" );
    console.log( "Object with id: " + this.getIdentity( event.item ) + " was added");
  });

  var myModel = new ForestStoreModel( {store:myStore, query:{type:"parent"}} );
  myModel.on( "dataValidated", function () {
    console.log( "Store data has been validated" );
  });

  var myTree  = new Tree( {model:myModel}, "CheckboxTree" };
  myTree.on( "load", function () {
    console.log( "The Tree has been loaded." );
  }
  myTree.startup();
                    ...
  myStore.put( {name:"Lisa", lastName:"Simpson", hair:"blond"} );
}
```

#### DOM-4 Events
If you want to learn more about the DOM-4 Events checkout
<a href="http://www.w3.org/TR/dom/#events" target="_blank">DOM Events</a>.
If you're interested in a dojo style implementation of a fully DOM-4 compliant Event
system checkout the event system as part of my
<a href="https://github.com/pjekel/indexedDB/tree/master/dom/event" target="_blank">IndexedDB</a>
project.


<h2 id="deleting-tree-nodes">Deleting Tree Nodes</h2>

In contrast to the standard dijit Tree, the CheckBox Tree offers the options to
delete tree nodes using the keyboard. However, the functionality is disabled by
default. There are two tree properties associated with this feature:

* enableDelete
* deleteRecursive

The **_enableDelete_** enables or disables the keyboard delete feature whereas
**_deleteRecursive_** determines if the descendants of the to be deleted item(s)
are to be removed from the store as well, the default is `false`.

To delete an item using the keyboard simply select the tree node(s) and press
the CTRL+DELETE keys.

<h2 id="sorting-tree-nodes">Sorting Tree Nodes</h2>

The order in which tree nodes are displayed depends on the model query and the
order of the store records. Lets assume we have the following set or records in
our store:

```javascript
{ name:"Root", parent:[] },

{ name:"Lisa", parent:["Homer","Marge"], hair:"blond", age: 7 },
{ name:"Bart", parent:["Homer","Marge"], hair:"blond", age: 8 },
{ name:"Maggie", parent:["Homer","Marge"], hair:"blond", age: 3 },
{ name:"Patty", parent:["Jacqueline"], hair:"blond", age: 40 },
{ name:"Selma", parent:["Jacqueline"], hair:"blond", age: 40 },
{ name:"Rod", parent:["Ned"], hair:"blond", age: 9 },
{ name:"Todd", parent:["Ned"], hair:"blond", age: 10 },
{ name:"Abe", parent:["Root"], hair:"none", age: 75 },
{ name:"Mona", parent:["Root"], hair:"none", age: 72 },
{ name:"Jacqueline", parent:["Root"], hair:"none", age: 70 },
{ name:"Homer", parent:["Abe","Mona"], hair:"none", age: 40 },
{ name:"Marge", parent:["Jacqueline"], hair:"none", age: 38 },
{ name:"Ned", parent:["Root"], hair:"none", age: 38 },
{ name:"Apu", parent:["Root"], hair:"black", age: 50 },
{ name:"Manjula", parent:[Apu], hair:"black", age: 52}
```

When we create the model using most of the model's default settings like:

```javascript
var model = new TreeStoreModel( { store: store, query: {name: "Root"} });
```
The root children will be displayed in the order they appear in the store, that is:
**_Abe, Mona, Jacqueline, Ned_** and finally **_Apu_**. The same goes for any children,
for example, expanding tree node 'Homer' displays **_Lisa, Bart_** and **_Maggie_**.

As of **_cbtree v0.9.4_** the Tree models have a new property called [options](Model-API#options)
which is a JavaScript key:value pairs object defining the sort order of the tree.
The options object is currently defined as follows:

```
options     = "{" "sort" ":" sortOptions "}"
sortOptions = "[" SortInfo ["," SortInfo]* "]" / Function
SortInfo    = "{" attribute ["," descending] ["," ignoreCase] "}"
attribute   = "attribute" ":" JSidentifier
descending  = "descending" ":" boolean
ignoreCase  = "ignoreCase" ":" boolean
boolean     = "true" / "false"
```
See also [sort directives](Store-API#wiki-sortDirective). For example:
```javascript
var options = {[
  {attribute: "name", descending: true, ignoreCase: true},
  {attribute: "hair", ignoreCase: true},
  {attribute: "age"}
]};
```
The value of both the _descending_ and _ignoreCase_ sort info properties defaults
to `false`.
The sort operation is performed in the order in which the sort information appears
in the sort options array. Given the example above, tree nodes are first sorted by
name followed by hair color and finally age.
Creating a model with the options property specified would look like:

```javascript
var mySortOptions = {sort: [
  {attribute: "name", descending: true, ignoreCase: true},
  {attribute: "hair", ignoreCase: true},
  {attribute: "age"}
]};
var model = new TreeStoreModel( { store: store,
                                  query: {name: "Root"},
                                  options: {sort: mySortOptions}
                                });
```
#### Using a custom sort function

Alternatively, instead of specifying the options sort property as an object you
can also specify a custom sort function. Consider the following example:
```javascript
function mySortFunc(itemA, itemB) {
  if (itemA.name > itemB.name) {
    return 1;
  } else if (itemA.name < itemB.name) {
    return -1;
  }
  return 0;
}
var model = new TreeStoreModel( { store: store, query: {name: "Root"}, options: {sort: mySortFunc}});
```
Here the custom sort function **_mySortFunc_** is called with two store items that matched
the associated model query. The custom sort function **_MUST_** return -1, 0 or 1. See
JavaScript [Array.prototype.sort](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array/sort)
for additional information.

<span class="mega-octicon octicon-alert"></span>Be aware that using the sort feature
overrides the store's optional **_before_** property when adding records to the store.
The store will still insert the record at the desired location but because the model
query results are sorted the significance of the actual location is lost to the model
and tree.