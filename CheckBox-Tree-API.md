<h3>Content <span class="mega-octicon octicon-book"></span></h3>
* [Tree Properties](#tree-properties)
* [Tree Functions](#tree-functions)
* [Tree Callbacks](#tree-callbacks)

<h2 id="tree-properties">Tree Properties</h2>

CheckBox Tree properties enable or disable specific features and/or define
characteristics of the tree.
The property names also represent properties in the keyword object passed to
the CheckBox Tree constructor. The following example illustrates how to apply
the CheckBox Tree properties:


```javascript
require(["cbtree/Tree", ... ], function ( Tree, ... ) {
  var keywordArgs = { model:myModel, persist:false, openOnClick:true };
  var myTree = new Tree( keywordArgs );
}
```

Additional CheckBox Tree Properties are available when the Tree Styling extension
is loaded, see [Tree Styling Properties](Tree-Styling#styling-properties)
for details.


<h3 id="autoexpand">autoExpand:</h3>
> **_TYPE_**: Boolean

> Fully expand the tree on load. Overrides [persist](#persist)

> **_DEFAULT_**: false


<h3 id="branchcheckbox">branchCheckBox:</h3>
> **_TYPE_**: Boolean

> If true, the checkbox associated with a tree branch will be displayed, otherwise the
> checkbox will be hidden but still available for checking its state. See also:
> [branchReadOnly](#branchreadonly)

> **_DEFAULT_**: true

<h3 id="branchicons">branchIcons:</h3>
> **_TYPE_**: Boolean

> Determines if the FolderOpen/FolderClosed icon or their custom equivalent is
> displayed. If false, the branch icon is hidden.

> **_DEFAULT_**: true

<h3 id="branchreadonly">branchReadOnly:</h3>
> **_TYPE_**: Boolean

> Determines if branch checkboxes are read only. If true, the user must explicitly
> check/uncheck every child checkbox individually. This property overrides the
> per store item 'enabled' features for any store item associated with a tree
> branch.

> **_DEFAULT_**: false

<h3 id="checkboxes">checkBoxes:</h3>
> **_TYPE_**: Boolean

> If true it enables the creation of checkboxes, If a tree node actually gets a
> checkbox depends on the configuration of the [model properties](Model-API#model-properties)
> If false no checkboxes will be created regardless of the model configuration.

> **_DEFAULT_**: true

> <span class="mega-octicon octicon-alert"></span>If checkBoxes is true,
> the model for the tree **MUST** support the [getChecked()](Model-API#wiki-getchecked)
> and [setChecked()](Model-API#wiki-setchecked) methods.

<h3 id="clickeventcheckbox">clickEventCheckBox:</h3>
> **_TYPE_**: Boolean

> If true, both the *click* and *checkBoxClick* events are generated when a checkbox
> is clicked. If false, only the *checkBoxClick* event is generated.

> **_DEFAULT_**: true

<h3 id="closeonunchecked">closeOnUnchecked:</h3>
> **_TYPE_**: Boolean

> If true, unchecking a branch node checkbox will close/collapse the branch. In addition,
> when all children of a given branch are unchecked the branch will also collapse, that is,
> if the model property [checkedStrict](Model-API#wiki-checkedstrict) is enabled (default).

> **_DEFAULT_**: false


<h3 id="deleterecursive">deleteRecursive:</h3>
> **_TYPE_**: Boolean

> Determines if a delete operation, initiated from the keyboard, should include
> all descendants of the selected item(s). If false, only the selected item(s)
> are deleted from the store. This property has only effect when the store property
> *enableDelete* is true.

> **_DEFAULT_**: false

<h3 id="enabledelete">enableDelete:</h3>
> **_TYPE_**: Boolean

> Determines if deleting tree nodes using the keyboard is allowed. By default
> items can only be deleted using the store interface. If set to true the user
> can also delete tree items by selecting the desired tree node(s) and pressing
> the CTRL+DELETE keys.

> **_DEFAULT_**: false


<h3 id="leaficons">leafIcons:</h3>
> **_TYPE_**: Boolean

> Determines if the Leaf icon, or its custom equivalent, is displayed. If false,
> the node or leaf icon is hidden.

> **_DEFAULT_**: true

<h3 id="leafreadonly">leafReadOnly:</h3>
> **_TYPE_**: Boolean

> Determines if leaf checkboxes are read only. If true, the  user can only
> check/uncheck branch checkboxes. This property overrides the per store item
> 'enabled' features for any store item associated with a tree leaf.

> **_DEFAULT_**: false

<h3 id="model">model:</h3>
> **_TYPE_**: Object

> Interface to read tree data, get notifications of changes to tree data, and
> for handling drop operations (i.e drag and drop onto the tree)

> **_DEFAULT_**: null

<h3 id="openonchecked">openOnChecked:</h3>
> **_TYPE_**: Boolean

> If true, checking a branch checkbox will open/expand the branch.

> **_DEFAULT_**: false

> <span class="mega-octicon octicon-alert"></span> This property is especially helpful
> when the tree is part of a form that you want to submit. Expanding checked branches
> will guarantee that all checked nodes will be included in the form submission.

<h3 id="openonclick">openOnClick:</h3>
> **_TYPE_**: Boolean

> If true, clicking a folder node's label will open it, rather than calling
> tree's callback method onClick().

> **_DEFAULT_**: false

<h3 id="openondblclick">openOnDblClick:</h3>
> **_TYPE_**: Boolean

> If true, double-clicking a folder node's label will open it, rather than
> calling tree's callback method onDblClick().

> **_DEFAULT_**: false

<h3 id="persist">persist:</h3>
> **_TYPE_**: Boolean

> Enables or disables the use of cookies for saving the tree state.

> **_DEFAULT_**: true

<h3 id="showRoot">showRoot:</h3>
> **_TYPE_**: Boolean

> Determines if the tree root is displayed or not. This property is specificly
> helpful when using a Forest Store Model and you don't want to display the
> fabricate root.

> **_DEFAULT_**: true

<h3 id="widget">widget:</h3>
> **_TYPE_**: [WidgetObject](#widget-object)

> Specifies the checkbox widget to be instanciated for the tree node. The
> default is the CheckBox Tree multi-state checkbox.

> **_DEFAULT_**: null



<h2 id="tree-functions">Tree Functions</h2>
### Accessors

As of dojo 1.6 all dijit widgets come with the so called auto-magic accessors
*get()* and *set()*. All CheckBox Tree API's, that is, the CheckBox Tree, Tree
Styling and Store Model API, use these accessors as their primary interface.
For example, to get the checked state of a tree node one could simply call:
*get("checked")* or to change the checked state call: *set("checked",true)*.

#### Note:
The property names *"checked"* and *"enabled"* are automatically mapped to the
appropriate store item properties based on the store models [checkedAttr](Model-API#wiki-checkedAttr)
and [enabledAttr](Model-API#wiki-enabledattr) values. Therefore, at the application
level you can simple  use the keywords *"checked"* and *"enabled"* regardless
of the actual store item properties.

***********************************************

<h3 id="get">collapseUnchecked( node )</h3>
> Collapse a branch node conditional. If the checkbox of the item associated with the
> node is unchecked, the node is closed/collapsed. See also the tree properties
> [closeOnUnchecked](#closeonunchecked) and [openOnChecked](#openonchecked) which enable
> automatic branch expanding and collapsing.

**_node:_** TreeNode

> The tree node to close/collapse. If the node argument is omitted the tree root node
> is used. Calling `collapseUnchecked()` without the node argument will collapse all
> unchecked branches in the tree.

***********************************************

<h3 id="get">get( propertyName )</h3>
> Returns the value of the tree or tree node property identified by *propertyName*.

**_propertyName:_** String

> Name of the tree or tree node property.

**returns:** any

> Returns the property value

***********************************************

<h3 id="get">expandChecked( node )</h3>
> Expand a branch node conditional. If the checkbox of the item associated with the node
> is checked, the node is opened/expanded. Checked child branches of the newly expanded
> branch will automatically expand as well. See also the tree properties
> [closeOnUnchecked](#closeonunchecked) and [openOnChecked](#openonchecked) which enable
> automatic branch expanding and collapsing.

**_node:_** TreeNode

> The tree node to open/expand. If the node argument is omitted the tree root node is
> used. Calling `expandChecked()` without the node argument will expand all checked
> branches in the tree.

***********************************************

<h3 id="mapeventtoattr">mapEventToAttr( oldPropName, property, nodeProp, value? )</h3>
> Map an object property name to a tree node property. If the optional *value*
> argument is specified, its value is assigned to the tree node property. If
> *value* is a function, the result returned by the function is assigned to the
> tree node property.

**_oldPropName:_** String
> Property name that is currently mapped to an tree node attribute. If present
> its entry in the mapping table is removed.

**_property:_** String
> Object property name to be mapped to a tree node property.

**_nodeProp:_** String
> Property name of a tree node to which argument *property* is mapped.

**_value:_** AnyType
> Optional, a fixed value to be assigned to *nodeProp* or a function. If value
> is a function, the function is called as `value(item, nodeProp, modelValue)`
> and the result returned is assigned to *nodeProp* instead. If the *value*
> argument is omitted, the event value passed from the model is used.

***********************************************

<h3 id="set">set( propertyName, newValue )</h3>
> Set the value of the tree or tree node property identified by *propertyName*.

**_propertyName:_** String
> Name of the tree or tree node property.

**_value:_** AnyType
> New value to be assigned.





<h2 id="tree-callbacks">Tree Callbacks</h2>

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

See [Working with Events](CheckBox-Tree-Usage#wiki-working-with-events) for a
detailed description.


<h2 id="widget-object">Widget Object</h2>

The optional CheckBox Tree property **_widget_** is an object with the following
properties:

#### type:
> **_TYPE_**: Widget | String

> The widget must support the accessors `get()` and `set()` and must have a
> *checked* property. In addition, the widgets should not stop any 'click'
> events calling `event.stop()` nor should it change any related widget
> instances without generating an 'click' event for those widgets.
> If *type* is a string, the string value must be a module ID. For example
> `dojox/form/TriStateCheckbox`

> **_DEFAULT_**: null

#### args:
> **_TYPE_**: Object?

> An optional JavaScript 'key:value' pairs object to be passed to the
> constructor of the widget.

> **_DEFAULT_**: null

#### target:
> **_TYPE_**: String

> Optional name of the target DOM node of a click event, The default is 'INPUT'.

> **_DEFAULT_**: "INPUT"

#### mixin:
> **_TYPE_**: Function

> Optional function called prior to instantiation of the widget. If specified,
> called as: `mixin( args )` were *args* is the list of arguments to be passed
> to the widget constructor. Argument *args* is the widget property *args* with
> the default arguments required by the CheckBox Tree mixed in. In the function
> body the "this" object equates to the enclosing node widget.

> **_DEFAULT_**: null

#### postCreate:
> **_TYPE_**: Function

> Optional function called immediately after the creation of the widget.
> If specified, called as: `postCreate()`. In the function body the "this" object
> equates to the newly created widget.

> **_DEFAULT_**: null

In the following example the ToggleButton widget is used instead of the default
CheckBox Tree multi-state checkbox. **Note:** this example also relies on the
Tree Styling extension being loaded to hide the tree node labels and icons.

```html
<script type="text/javascript">
  require([
    "dojo/ready",
    "dijit/form/ToggleButton",
    "cbtree/Tree",                      // CheckBox Tree
    "cbtree/store/ObjectStore",         // Eventable Object Store
    "cbtree/model/ForestStoreModel",    // Tree Forest Store Model
    "cbtree/extensions/TreeStyling"     // Tree Styling extension
    ], function( ready, ToggleButton, Tree, ObjectStore, ForestStoreModel ) {

      var store = new ObjectStore( { url: "../../datastore/Simpsons.json" });
      var model = new ForestStoreModel({ store: store,
                                         query: {type: 'parent'},
                                         rootLabel: 'The Simpsons',
                                         checkedRoot: true
                                       });
      ready( function() {
        var tree = new Tree( { model: model,
                               id: "MyTree",
                               widget: { type: ToggleButton,
                                         args:{iconClass:'dijitCheckBoxIcon'},
                                         mixin: function(args) {
                                            args['label'] = this.label;
                                         }
                                       }
                               }, "CheckBoxTree" );
        // Hide Labels and Icons for the entire tree.
        tree.set("labelStyle", {display:'none'});
        tree.set("iconStyle", {display:'none'});

        tree.startup();
      });
    }
  );
</script>
```



