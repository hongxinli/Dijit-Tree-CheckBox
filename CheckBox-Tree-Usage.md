
<h3>Content <span class="mega-icon mega-icon-readme"></span></h3>
* [Working with Events](#working-with-events)

<h2 id="working-with-events">Working with Events</h2>

With dojo 1.7 a new event handling module was introduced called `dojo/on`. 
The intend of this module is/was to make event handling with dojo easier and 
mimic some of the DOM4 Event system. However, `dojo/on` is far from a self 
contained DOM-4 Event system implementation.  
In addition, dijit widgets implement a modified version of `dojo/on` to make it
backward compatible with callback methods and non-DOM compliant events.
The `dijit/_WidgetBase.on()` method tries to map event names, case insensitive
that is, to callback methods whose name starts with "on" like *onClick()* or 
*onDblClick()*.

The next sections show and discuss the difference in implementation of callbacks
and events.

<h3>
	<span class="mega-icon mega-icon-arr-collapsed" style="margin-left:-8px;"></span>Callbacks
</h3>

Before we establish an event listener with a dijit widget we must fist check if
the widget has a method that could be auto linked with the event name. 
For example, if I want to listen for an event named "myEvent", does the widget
have a method called "onMyEvent" or "onmyevent"? If so, we need to define our
event listener as a callback function with the same set of argument as the 
widget's onMyEvent() method.

let's assume our widget has a method (callback) called `onMyEvent()` with a
signature like: `onMyEvent( item, node, event )` in which case we can establish
our event listener in two ways:

1. Using *dojo/aspect* or,
2. Call the widget's *on()* method.

##### Using dojo/aspect.
Using `dojo/aspect` we attach *after* advice to the widget method so our event
listener gets called each time the widget calls `onMyEvent()` like:

```javascript
require(["dojo/aspect", "cbtree/Tree", ... ], function (aspect, Tree, ... ) {
		function clickEvent( item, node, event ) {
			console.log( "A Tree Node was clicked" );
		}
                   ...
		var myTree  = new Tree( {model:someModel, ... } );
		aspect.after( myTree, "onMyEvent", clickEvent, true );
                   ...
})
```
<span class="mega-icon mega-icon-exclamation"></span> Make sure the fourth
argument is set to **_true_** otherwise `clickEvent()` is not called with the
original arguments of `onMyEvent()` but with the result instead. Also, notice
that when calling *aspect.after()* we specify the callback name "onMyEvent"
and not an event name.

##### Using widget on() method.
Using the widget's on() method we specify the event name "myEvent" and not the
callback name, like:

```javascript
require(["cbtree/Tree", ... ], function (Tree, ... ) {
		function clickEvent( item, node, event ) {
			console.log( "A Tree Node was clicked" );
		}
                   ...
		var myTree  = new Tree( {model:someModel, ... } );
		myTree.on( "myEvent", clickEvent );
                   ...
})
```
The above example accomplishes exactly the same thing as dojo/aspect did in the
first example simply because the _WidgetBase.on() method is able to map the event
name "myEvent" to the callabck "onMyEvent" and as a result calls dojo/aspect on
your behalf.

<h3>
	<span class="mega-icon mega-icon-arr-collapsed" style="margin-left:-8px;"></span>Events
</h3>

This time, let's assume our widget does **_NOT_** have a callback that could be auto
linked to the event name "myEvent". Instead the widget actually emits events of
type "myEvent". In this case both dojo/aspect and the widget.on() method will 
automatically create the callback, which in our case will be named "onMyEvent".

The big difference between the widget calling callback functions programmatically
and emitting events is the way the event listener will be called. In the latter 
case the event handlers are called with just one argument, the event.

```javascript
require(["cbtree/Tree", ... ], function (Tree, ... ) {
		function clickEvent( event ) {
			console.log( "A Tree Node was clicked" );
		}
                   ...
		var myTree  = new Tree( {model:someModel, ... } );
		myTree.on( "myEvent", clickEvent );
                   ...
})
```
In the above example notice that the function *clickEvent()* now is called with
just a single argument **_event_**. The event is a JavaScript key:value pairs
object.


If you interested in a dojo style implementation of a fully DOM-4 compliant Event 
system checkout the event system as part of my [IndexedDB](https://github.com/pjekel/indexedDB/tree/master/dom/event)
project.

<h3 id="tree-events">Tree Events</h3>


<table>
	<tr>
		<th>Event</th>
		<th>Callback</th>
		<th>Arguments</th>
		<th>Description</th>
	</tr>
	<tr>
		<td>click</td>
    <td>onClick</td>
    <td>(item, node, event)</td>
		<td>
			The tree node is clicked.
		</td>
	</tr>
	<tr>
		<td>checkBoxClick</td>
    <td>onCheckBoxClick</td>
    <td>(item, node, event)</td>
		<td>
			Checkbox got clicked.
		</td>
	</tr>
	<tr>
		<td>close</td>
    <td>onClose</td>
    <td>(item, node)</td>
		<td>
			Node closed.
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
			Node opened
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




<h3 id="model-events">Model Events</h3>

<table>
	<tr>
		<th>Event</th>
		<th>Callback</th>
		<th>Arguments</th>
		<th>Description</th>
	</tr>
	<tr>
		<td>change</td>
    <td>onChange</td>
    <td>(item, property, newValue, oldValue)</td>
		<td>
			Property of a store item changed.
		</td>
	</tr>
	<tr>
		<td>childrenChange</td>
    <td>onChildrenChange</td>
    <td>(parent, newChildrenList)</td>
		<td>
			The children of an node changed.
		</td>
	</tr>
	<tr>
		<td>delete</td>
    <td>onDelete</td>
    <td>(item)</td>
		<td>
			Store item removed
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
		<td>dataValidated</td>
    <td>onDataValidated</td>
    <td>(void)</td>
		<td>
			The store data has been validated.
		</td>
	</tr>
</table>

<h3 id="store-events">Store Events</h3>

<table>
	<tr>
		<th>Event</th>
		<th>Callback</th>
		<th>Argument</th>
		<th>Description</th>
	</tr>
	<tr>
		<td>change</td>
    <td></td>
    <td>(event)</td>
		<td>
			Property of a store item changed.
		</td>
	</tr>
	<tr>
		<td>new</td>
    <td></td>
    <td>(event)</td>
		<td>
			Store item added
		</td>
	</tr>
	<tr>
		<td>remove</td>
    <td></td>
    <td>(event)</td>
		<td>
			Store item removed/deleted.
		</td>
	</tr>
</table>
