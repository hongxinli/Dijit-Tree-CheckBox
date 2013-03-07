
<h3>Content <span class="mega-icon mega-icon-readme"></span></h3>
* [Model Properties](#model-properties)
* [Model Functions](#model-functions)
* [Model Callbacks](#model-callbacks)

<h2 id="model-properties">Model Properties</h2>

Model properties define specific features or characteristics of a store model.
The property names also represent properties in the keyword object passed to 
the model constructor. For example:

```javascript
require(["cbtree/model/TreeStoreModel", ... ], function (TreeStoreModel, ... ) {
  var keywordArgs = { store:myStore, checkedAll:true, query:{type:"Monarch"} };
  var model = new TreeStoreModel( keywordArgs );
}
```

<h3 id="checkedAll">checkedAll:</h3>
> **_TYPE_**: Boolean

> If true, every store object will receive a so-called 'checked' state property.
> The name of the actual property is defined by the value of the model property 
> **_checkedAttr_**. If the 'checked' state property is added to a store object,
> its initial state is defined by the value of the model property **_checkedState_**
> (see also *checkedAttr*)

> **_DEFAULT_**: true

<h3 id="checkedattr">checkedAttr:</h3>
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

<h3 id="checkedstate">checkedState:</h3>
> **_TYPE_**: Boolean

> The default *checked* state applied to every store item unless otherwise
> specified in the object store (see also: *checkedAttr*)

> **_DEFAULT_**: false

<h3 id="checkedroot">checkedRoot:</h3>
> **_TYPE_**: Boolean

> If true, the tree root node will receive a checked state even though it may not 
> represent an actual entry in the store. This property is independent of the
> tree property **_showRoot_**. If the tree property *showRoot* is set to false the
> checked state for the root will not show either.

> **_DEFAULT_**: false

<h3 id="checkedstrict">checkedStrict:</h3>
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

<h3 id="enabledattr">enabledAttr:</h3>
> **_TYPE_**: String

> The name of the store object property that holds the 'enabled' state of the
> checkbox or alternative widget. Note: Eventhough it is referred to as the
> 'enabled' state the tree will only use this property to enable/disable the 
> 'ReadOnly' property of a checkbox or alternative widget. This because disabling
> a widget (DOM element) may exclude it from HTTP POST operations.

> **_DEFAULT_**: null

<h3 id="iconattr">iconAttr:</h3>
> **_TYPE_**: String

> The name of the store object property whose value is considered a tree node
> icon (css class name). If specified, get the icon from an object using this
> property name. (see [Tree Styling](Tree-Styling))

> **_DEFAULT_**: ""

<h3 id="labelattr">labelAttr:</h3>
> **_TYPE_**: String

> The name of the store object property whose value is returned as the label
> for the tree node.

> **_DEFAULT_**: "name"

<h3 id="multistate">multiState:</h3>
> **_TYPE_**: Boolean

> Determines if the object checked state is to be maintained as multi state or 
> as dual state, that is, {"mixed", true, false} versus {true, false}. If true
> multi state is enabled.

> **_DEFAULT_**: true

<h3 id="normalize">normalize:</h3>
> **_TYPE_**: Boolean

> If true, the checked state of any non branch (leaf) checkbox is normalized, 
> that is, true or false. When normalization is enabled checkboxes associated
> with tree leafs (e.g. nodes without children) can never have a "mixed" state.

> **_DEFAULT_**: true

<h3 id="parentproperty">parentProperty:</h3>
> **_TYPE_**: String

> The property name of a store object whose value represents the object's parent
> id or ids. If the store assigned to the model has a **_parentProperty_** 
> property the store value is used.

> **_DEFAULT_**: "parent"

<h3 id="query">query:</h3>
> **_TYPE_**: Object

> A JavaScript key:value pairs object used the query the store to determine the
> tree root object or, in case the query returns multiple objects, the children
> of the fabricated root object. For example: `{type:"parent"}`.
> If not specified, a wildcard search is performed using the store's 
> **_idProperty_** property value. (See also [Tree Store Model](#tree-store-model)
> and [Forest Store Model](#forest-store-model))

> **_DEFAULT_**: null

<h3 id="rootlabel">rootLabel:</h3>
> **_TYPE_**: String

> Label of the fabricated tree root object ([Forest Store Model](#forest-store-model)
> only) or the alternative label in case the tree root is represented by a store
> object ([Tree Store Model](#tree-store-model) only)

> **_DEFAULT_**: null

<h3 id="rootid">rootId:</h3>
> **_TYPE_**: String

> ID of the fabricated root item, Only valid for a Forest Store Model.

> **_DEFAULT_**: "$root$"

<h3 id="store">store:</h3>
> **_TYPE_**: Object

> The underlying object store.

> **_DEFAULT_**: null








<h2 id="model-functions">Model Functions</h2>

The following section list the default functions available with the CheckBox Tree
store models.


<h3 id="getchecked">getChecked ( item )</h3>
> Get the current checked state from the object store. The checked state
> in the store can be: 'mixed', true, false or undefined. Undefined in this
> context means the object has no checked property (see checkedAttr).

**_item:_** Object
> A valid store object.

**returns:** Boolean | String | undefined

*********************************************

<h3 id="getchildren">getChildren( parent, onComplete, onError )</h3>
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

<h3 id="getenabled">getEnabled( item )</h3>
> Returns the current 'enabled' state of an item as a boolean. See the *enabledAttr*
> property description for more details.

**_item:_** Object
> A valid store object.

**returns:** Boolean
> The state returned is the value of the checked widget "readOnly" property.

*********************************************

<h3 id="geticon">getIcon( item )</h3>
> If the *iconAttr* property of the model is set, get the icon for *item* from
> the store otherwise *undefined* is returned.

**_item:_** Object
> A valid store object.

**returns:** Object | String
> Returns an [icon](Tree-Styling#wiki-icon-properties) object or a string

*********************************************

<h3 id="getidentity">getIdentity( item )</h3>
> Get the identity of an item.

**_item:_** Object
> A valid store object.

**returns:** String | Number

*********************************************

<h3 id="getlabel">getLabel( item )</h3>
> Get the label for an item. If the model property *labelAttr* is set, the
> associated property of the item is retrieved otherwise the *labelAttr*
> property of the store is used.

**_item:_** Object
> A valid store object.

**returns:** String

*********************************************

<h3 id="getparents">getParents ( item )</h3>
> Get the parent(s) of a store item. Returns an array of store items.	

**_item:_** Object
> A valid store object.

**returns:** dojo/promise/Promise
> Returns a promise. If resolved the result of the promise is an array of parent
> objects.

*********************************************

<h3 id="getroot">getRoot( onItem, onError? )</h3>
> Calls onItem with the root item for the tree, possibly a fabricated item.
> Calls onError on error.

**_onItem:_** Function
> Callback function. On successful completion called as *onItem( root )*.

**_onError:_** Function
> Called when an error occured.

**returns:** void

*********************************************

<h3 id="isitem">isItem( something )</h3>
> Returns true if *something* is an item and came from this model instance.
> Returns false if *something* is a literal, an item from another model instance,
> or is any object other than an item.

**_something:_** Any

**returns:** Boolean

*********************************************

<h3 id="mayhavechildren">mayHaveChildren( item )</h3>
> Returns true if an item has or may have children.

**_item:_** Object
> A valid store object.

**returns:** Boolean

*********************************************

<h3 id="setchecked">setChecked ( item, newState )</h3>
> Update the checked state of a store item. If the model property *checkedStrict*
> is true, the items parent(s) and children, if any, are updated accordingly.

**_item:_** Object
> A valid store object.

**_newState:_** Boolean | String
> The new checked state. The state can be either a boolean (true | false) or a string ('mixed')

**returns:** void

*********************************************

<h3 id="setenabled">setEnabled( item, value )</h3>

> Set the new 'enabled' state of an item. See the *enabledAttr* property description for more
> details.

**_item:_** Object
> A valid store object.

**_value:_** Any

**returns:** void





<h2 id="model-callbacks">Model Callbacks</h2>

The CheckBox Tree models, although not widgets, offer the same `on()`
functionality as widgets do. Therefore you can simple register a model event
listener like `model.on( "dataValidated", myEventHandler );`

The tables below list the event names and associated callback. If for any given
event type a callback is specified, the arguments column specifies the list of
arguments passed to the event listener.

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
			Store item removed
		</td>
	</tr>
	<tr>
		<td>pasteItem</td>
    <td>onPasteItem</td>
    <td>(item, insertINdex, before)</td>
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
</table>


See [Working with Events](CheckBox-Tree-Usage#wiki-working-with-events) for a
detailed description.


