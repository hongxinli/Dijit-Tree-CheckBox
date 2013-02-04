
The CheckBox Tree is an extension of the standard dijit tree, therefore all
features available with the dijit tree are also offered by the CheckBox Tree
with the exception of dijit Tree V1.0 backward compatibility, that is, you must 
provide the **_model_** argument when creating the tree.
This document describes the CheckBox Tree extensions only. Details of the
standard dijit tree can be found 
[here](http://dojotoolkit.org/reference-guide/1.7/dijit/Tree.html).

<h3>Content <span class="mega-icon mega-icon-readme"></span></h3>

* [CheckBox Tree Basics](#checkbox-tree-basics)
* [CheckBox Tree Properties](#checkbox-tree-properties)
* [CheckBox Tree API](#checkbox-tree-api)
* [Callbacks and Events](#callbacks-and-events)
* [CheckBox Tree Placement](#checkbox-tree-placement)
* [CheckBox Tree Advanced](#checkbox-tree-advanced)
* [Sample Application](#sample-application)

<h2 id="checkbox-tree-basics">CheckBox Tree Basics</h2>

### CheckBoxes & Tree Store Models 

The CheckBox Tree is implemented using the Model-View-Controller (MVC) pattern.
The tree component, the part you actually see in your browser, is considered the
'View' or presentation layer.
The tree offers the ability to instantiate a checkbox for tree nodes but does
not control whether or not a tree node is eligible to get a checkbox, that is
actually determined by the model. The CheckBox Tree comes with three 
[Store Models](Store-Models):

* [TreeStoreModel](Store-Models#wiki-tree-store-model)
* [ForestStoreModel](Store-Models#wiki-forest-store-model)
* [FileStoreModel](Store-Models#wiki-file-store-model)

The [model properties](Store-Models#store-model-properties) and configuration 
determine checkbox eligibility for tree nodes. By default, all models are configured 
to generate a checkbox for every tree node. 

If you are planning to write your own model, please refer to **/cbtree/model/api/model.js**
for the model interface definition.

### CheckBox vs Checked State 

The model provides the primary interface to get or set the checked state for any
data item by means of the **_getChecked()_** and **_setChecked()_** methods. 
In general, a model refers to the 'checked' state rather than a checkbox state
simply because a checkbox is just the visual representation of a state. As a 
case in point, one could simply replace the default checkbox with a third party
widget, using the trees *widget* property, as long as the widget is capable of 
representing a 'checked' state. (See [Mixing in other Widgets](#mixing-in-other-widgets)
for an example).

Although you can get or set the checked state of a tree node using *get("checked")* 
or *set("checked", false)*, *checked* is not an actual property of a tree node instead
the request is redirected to the models *getChecked()* and *setChecked()* methods.

### Parent-Child Relationship
One of the Store Model features is the ability to maintain a parent-child relationship.
The parent checked state, represented as a tree branch checkbox, is the composite
state of all its child checkboxes. For example, if the child checkboxes are either
all checked or unchecked the parent will get the same checked or unchecked state.
If however, the children checked state is mixed, that is, some are checked while
others are unchecked, the parent will get a so called 'mixed' state.

### CheckBox Tree Styling
The Tree Styling extension allows you to dynamically manage the tree styling 
on a per data item basis. Using the simple to use accessor **_set()_** you can
alter the icon, label and row styling for the entire tree or just a single data
object. For example: *set("iconClass", "myIcon", item)* changes the css icon 
class associated with all tree node instances of data object *item*, or if you 
want to change the label style:

```javascript
// As a callback of a CheckBox click event
function checkBoxClicked( item, nodeWidget, evt ) {
  if (nodeWidget.get("checked")) {
    tree.set("labelStyle", {color:"red"}, item);
  }
}

// As a regular function
function updateStyle( item ) {
  if (model.getChecked( item )) {
    tree.set("labelStyle", {color:"red"}, item);
  }
}
```
### Store Model API

As stated before, the CheckBox Tree comes with an optional [Store Model API](Store-Model-API)
which serves both the TreeStoreModel as well as the ForestStoreModel. The Store
Model API allows the user to programmatically build and maintain checkbox trees.
For example, you can create your store starting with an empty JSON dataset or use
an existing data store and use the API to add, remove or change store items.
In addition, you can simply check/uncheck a single store item or a set of store
items using a store query.  

<span class="mega-icon mega-icon-exclamation"></span> This version of the cbtree 
no longer supports the *setItemAttr()* and *getItemAttr()* methods, to change
store object properties use the appropriate store interface. Some of the Store 
Model API methods are:

* check(), uncheck()
* getChecked(), setChecked()
* fetchItem(), fetchItemsWithChecked()
* addParent(), removeParent()

For a detailed description of all API methods, please refer to [Store Model API](Store-Model-API). 

<h2 id="checkbox-tree-properties">CheckBox Tree Properties</h2>

In addition to the standard dijit tree properties the CheckBox Tree has the
following set of public properties. For each property the type and default
value is listed. 

#### branchIcons:
> **_TYPE_**: Boolean

> Determines if the FolderOpen/FolderClosed icon or their custom equivalent is
> displayed. If false, the branch icon is hidden.

> **_DEFAULT_**: true

#### branchReadOnly:
> **_TYPE_**: Boolean

> Determines if branch checkboxes are read only. If true, the user must explicitly
> check/uncheck every child checkbox individually. This property overrides the
> per store item 'enabled' features for any store item associated with a tree
> branch.

> **_DEFAULT_**: false

#### checkBoxes:
> **_TYPE_**: Boolean

> If true it enables the creation of checkboxes, If a tree node actually gets a
> checkbox depends on the configuration of the [model properties](Store-Models#store-model-properties)
> If false no checkboxes will be created regardless of the model configuration.

> **_DEFAULT_**: true

> <span class="mega-icon mega-icon-exclamation"></span>If checkBoxes is true, 
> the model for the tree **MUST** support the getChecked() and setChecked() 
> methods.

#### leafReadOnly:
> **_TYPE_**: Boolean

> Determines if leaf checkboxes are read only. If true, the  user can only
> check/uncheck branch checkboxes. This property overrides the per store item 
> 'enabled' features for any store item associated with a tree leaf.

> **_DEFAULT_**: false

#### nodeIcons:
> **_TYPE_**: Boolean

> Determines if the Leaf icon, or its custom equivalent, is displayed. If false,
> the node or leaf icon is hidden.

> **_DEFAULT_**: true

#### widget:
> **_TYPE_**: Object

> Specifies the checkbox widget to be instanciated for the tree node. The 
> default is the CheckBox Tree multi-state checkbox. (see [mixin widgets](#mixing-in-other-widgets)
> for details).

> **_DEFAULT_**: null

##### Example
The following example illustrates how to apply the CheckBox Tree properties.
Please note that if the default property value is used the property can be
omitted, therefore the example below is for demonstation purposes only.

```javascript
require(["cbtree/Tree",
         "cbtree/model/TreeStoreModel",
         "cbtree/store/Hierarchy",
                ...
         ], function( Tree, TreeStoreModel, Hierarchy, ... ) {
                ...
    var myTree = new Tree( {
            model: model,
            id: "MenuTree",
            branchIcons: true,
            branchReadOnly: false,
            checkBoxes: true,
            nodeIcons: true,
                ...
            });
```

Additional CheckBox Tree Properties are available when the Tree Styling extension
is loaded, see [Tree Styling Properties](Tree-Styling#styling-properties) 
for details.

<h2 id="checkbox-tree-api">CheckBox Tree API</h2>
### Accessors

As of dojo 1.6 all dijit widgets come with the so called auto-magic accessors
*get()* and *set()*. All CheckBox Tree API's, that is, the CheckBox Tree, Tree
Styling and Store Model API, use these accessors as their primary interface.
For example, to get the checked state of a tree node one could simply call: 
*get("checked")* or to change the checked state call: *set("checked",true)*.

#### Note:
The property names *"checked"* and *"enabled"* are automatically mapped to the 
appropriate store item properties based on the store models *checkedAttr* and
*enabledAttr* values. Therefore, at the application level you can simple  use
the keywords *"checked"* and *"enabled"* regardless of the actual store item
properties.
 
***********************************************
#### get( propertyName )
> Returns the value of the tree or tree node property identified by *propertyName*.

**_propertyName:_** String
> Name of the tree or tree node property.

**returns:** any
> Returns the property value

***********************************************
#### set( propertyName, newValue )
> Set the value of the tree or tree node property identified by *propertyName*.

**_propertyName:_** String
> Name of the tree or tree node property.

**_value:_** AnyType
> New value to be assigned.

<h2 id="callbacks-and-events">Callbacks and Events</h2>
There are two ways to capture tree events, either establish an event listener 
(the preferred method) or connect to the callback method associated with an event,
assuming there is one. The name of the callback is typically the camelcase name
of the event prefixed with the word 'on'. For example, the callback name for 
the event **checkBoxClick** is **_onCheckBoxClick_**.

See also [Working with Events](CheckBox-Tree-Usage#wiki-working-with-events) for
a more detailed description of events in general.

***********************************************
#### onCheckBoxClick( item, nodeWidget, evt )
> Callback routine called each time a checkbox is clicked.

**_item:_** Object
> The data object associated with the nodeWidget whose checkbox got clicked.

**_nodeWidget:_** dijit.widget
> The tree node widget whose checkbox was clicked.

**_evt:_** Object
> A dojo or native HTML event object.

##### Examples

To establish an event listener, call the tree **_on()_** method with the event
name.

```javascript
require(["cbtree/Tree",  ... ], function ( Tree, ... ) {
  function checkBoxClicked( item, nodeWidget, evt ) {
    alert( "The new state for " + this.getLabel(item) + " is: " + nodeWidget.get("checked") );
  }
            ...
  var myTree = new Tree( ... );

  myTree.on( "checkBoxClick", checkBoxClicked );
});
```

To connect to the callback, call **_dojo/aspect()_** with the name of the callback.
```javascript
require(["dojo/aspect", "cbtree/Tree", ... ], function (aspect, Tree, ... ) {
  function checkBoxClicked( item, nodeWidget, evt ) {
    alert( "The new state for " + this.getLabel(item) + " is: " + nodeWidget.get("checked") );
  }
            ...
  var myTree = new Tree( ... );

  aspect.after( myTree, "onCheckBoxClick", checkboxClicked, true );
});
```
**Note:** the use of _dojo/aspect_ to connect to tree callbacks is likely to be 
deprecated in the future.


<h2 id="checkbox-tree-placement">CheckBox Tree Placement</h2>

There are different ways to place the CheckBox Tree in the DOM. The easiest way 
would be to specify the parent DOM node when creating the CheckBox Tree. Lets 
assume you have a `<div>` element defined in your document as follows:

```html
<div id="CheckBoxTree" style="width:300px; height:100%; border-style:solid; border-width:medium;">
</div>
```

Notice the `<div>` element has a width, height and border property specified.  

The first option is to create the CheckBox Tree and specify "CheckBoxTree" as 
the parent DOM node like:

```javascript
var myTree = new cbtree( { ... }, "CheckBoxTree" };
myTree.startup();
```
  
The above method however, replaces the existing DOM node with a new one resulting in 
the loss of all the style properties we previously defined. Alternatively you can
insert your tree as a child node of CheckBoxTree, preserving all of its properties,
as follows:

```javascript
var myTree = new cbtree( { ... } };
myTree.placeAt( "CheckBoxTree" );
myTree.startup();
```

You can also specify the location of the child node as a second parameter to the 
*placeAt()* method like:

```javascript
myTree.placeAt( "CheckBoxTree", "last" );
myTree.startup();
```

The location is relative to the other child nodes of the parent node. The placeAt()
location parameter accepts: *"after"*, *"before"*, *"replace"*, *"only"*, *"first"*
and *"last"*. The default location is *"last"*.
See the **_place()_** method of dojo/dom-construct for more details.

Please note that as of dojo 1.8 it is required to call the tree widget startup()
method after creating a tree.

<h2 id="checkbox-tree-advanced">CheckBox Tree Advanced</h2>

### Mapping Model Events

The CheckBox Tree is completely event driven and after instantiation only acts
upon events generated by the model by mapping those types of events to tree node
properties such as 'label' or 'checked'. Any event that cannot be mapped to a 
tree node property is, by default, ignored by the tree. For example, if a data
object has a property called 'age' and when the property is updated, the store 
and model will generate an event accordingly however, the tree does not known
how the 'age' property of the data objetc relates to any of the tree node 
properties and therefore ignores the event.

However, the CheckBox Tree offers an additional method to map item update events,
generated by the store and model, to tree node properties.

***********************************************
#### mapEventToAttr( oldPropName, property, nodeProp, value? )
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

##### Example  
The following example maps the **_age_** property of a data object to the tree
node property *label* resulting in a new label text each time a checkbox is
clicked. 

```html
<script type="text/javascript">
  require([
    "dojo/ready",
    "cbtree/Tree",
    "cbtree/model/ForestStoreModel", 
    "cbtree/store/Hierarchy",          // Hierarchy store
    "cbtree/store/Eventable"           // Eventable
    ], function( ready, Tree, ForestStoreModel, Hierarchy, Eventable ) {
        var store, model, tree;
        
        function makeLabel(item, attr, value ) {
          //  Action routine called when the tree recieved an update event from the model
          //  indicating the 'age' attribute of a store item has changed.
          var labelText = this.getLabel(item) + ' is now ' + value + ' years old';
          return labelText;
        }
        
        function checkBoxClicked( item, nodeWidget, evt ) {
          //  Action routine called when a checkbox is clicked.
          item.age++;
          store.put( item );
        }

        // Declare a simple JSON data set and create the model and tree.
        var dataSet = [ {name:'Homer', age:'45'}, {name:'Marge', age:'40'} ];

        // Create store and make it eventable. (could have used ObjectStore instead).
        store = Eventable( new Hierarchy( {data: dataSet, idProperty:"name"}) );
        model = new ForestStoreModel( {store: store, rootLabel:'The Family'});

        ready( function() {
          tree  = new Tree( {model: model, id:'MyTree'}, "CheckboxTree" );
          tree.on( "checkBoxClick", checkBoxClicked );

          // Map an 'age' property update event to the tree node 'label' propery and
          // call the action routine makeLabel() to get the label text.
          
          tree.mapEventToAttr( null,'age','label', makeLabel );
          tree.startup();
        });
     }
  );
</script>
```

In the above example the following sequence of events take place:

1.   Each time a checkbox is clicked the function *checkBoxClicked()* is called
  and the *age* property of *item* is incremented followed by a store update.
2.  As a result of the store update in step 1 the store will generate an *age* 
	property update event for the given item which is then forwarded to the tree
	by the model.
3.  When the CheckBox Tree recieves the update event it maps the *age* property
  to the tree node property *label* and calls the function *makeLabel()* to get
  the new label text.
4.  Finally, the CheckBox Tree internally calls *set("label", labelText)* for
	each tree node associated with the data item.

<h3 id="mixing-in-other-widgets">Mixing in other Widgets</h3>

By default the CheckBox Tree uses its own multi state checkbox widget to represent
a data items checked state. However, the CheckBox Tree also allows you to mixin
other widgets which are capable of representing a checked state using the tree 
**_widget_** property.

The optional CheckBox Tree property **_widget_** is an object with the following
properties:

#### type:
> **_TYPE_**: Widget | String

> The widget must support the accessors *get()* and *set()* and must have a
> *checked* property. In addition, the widgets should not stop any onClick'
> events calling *event.stop()* nor should it change any related widget
> instances without generating an onClick event for those widgets.
> If *type* is a string, the string value must be a module ID. For example 
> "dojox/form/TriStateCheckbox"

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
    "cbtree/extensions/Styling"         // Tree Styling extension
    ], function( ready, ToggleButton, Tree, ObjectStore, ForestStoreModel ) {

      var store = new ObjectStore( { url: "../../datastore/Simpsons.json" });
      var model = new ForestStoreModel( {
                                store: store,
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

<h2 id="sample-application">Sample Application</h2>
### Programmatically
The following is a basic sample of how to create a model, associate the model
with the tree and display the tree itself. In addition, the sample application
listens for the *checkBoxClick* event of the tree and as a result every time
a checkbox is clicked the function *checkBoxClicked()* is called.

```html
<!DOCTYPE html>
<html>
  <head>
    <meta charset="utf-8">
    <title>The CheckBox Tree with multi-parented Eventable Store</title>
    <style type="text/css">
      @import "../../../dijit/themes/claro/claro.css";
      @import "../../themes/claro/claro.css";
    </style>

    <script type="text/javascript">
      var dojoConfig = {
            async: true,
            parseOnLoad: true,
            isDebug: false,
            baseUrl: "../../../",
            packages: [
              { name: "dojo",  location: "dojo" },
              { name: "dijit", location: "dijit" },
              { name: "cbtree",location: "cbtree" }
            ]
      };
    </script>

    <script type="text/javascript" src="../../../dojo/dojo.js"></script>
    <script type="text/javascript">
      require([
        "dojo/ready",
        "cbtree/Tree",                     // Checkbox tree
        "cbtree/model/TreeStoreModel",     // Object Store Tree Model
        "cbtree/store/ObjectStore"         // Evented Object Store with Hierarchy
        ], function( ready, Tree, ObjectStoreModel, ObjectStore) {

          var store = new ObjectStore( { url:"../../store/json/Simpsons.json", idProperty:"name"});
          var model = new ObjectStoreModel( { store: store,
                                               query: {name: "Root"},
                                               rootLabel: "The Family",
                                               checkedRoot: true
                                             });

          function checkBoxClicked( item, nodeWidget, event ) {
            alert( "The new state for " + this.getLabel(item) + " is: " + nodeWidget.get("checked") );
          }

          ready(function(){
            var tree = new Tree( { model: model, id: "FamilyTree" }, "CheckboxTree" );
            tree.on( "checkBoxClick", checkBoxClicked);
            tree.startup();
          });
        });
      </script>

  </head>

  <body class="claro">
    <h1 class="DemoTitle">The CheckBox Tree with Multi State CheckBoxes</h1>
    <div id="CheckboxTree">
    </div>
  </body>
</html>
```

### Declarative
Following is the same program but this time we instanciate all elements declarative
using the HTML5 custom  *data-dojo*-xxx attributes. Notice that because we take the
declarative route we must explicitly include *dojo/parser* to parse our document.
The dojo parser will process all *data-dojo*-xxx attributes, without the parser
loaded you will not see the tree in your browser.

```html
<!DOCTYPE html>
<html>
  <head>
    <meta charset="utf-8">
    <title>The CheckBox Tree with multi-parented Eventable Store</title>
    <style type="text/css">
      @import "../../../dijit/themes/claro/claro.css";
      @import "../../themes/claro/claro.css";
    </style>

    <script type="text/javascript">
      var dojoConfig = {
            async: true,
            parseOnLoad: true,
            isDebug: false,
            baseUrl: "../../../",
            packages: [
              { name: "dojo",  location: "dojo" },
              { name: "dijit", location: "dijit" },
              { name: "cbtree",location: "cbtree" }
            ]
      };
    </script>

    <script type="text/javascript" src="../../../dojo/dojo.js"></script>
    <script type="text/javascript">
      require([
          "dojo/parser",
          "cbtree/Tree",                     // Checkbox tree
          "cbtree/model/TreeStoreModel",     // Object Store Tree Model
          "cbtree/store/ObjectStore"         // Evented Object Store with Hierarchy
        ]);

        function checkBoxClicked( item, nodeWidget, event ) {
          alert( "The new state for " + this.getLabel(item) + " is: " + nodeWidget.get("checked") );
        }
      </script>

  </head>
  <body class="claro">
    <h1 class="DemoTitle">Dijit Tree with Multi State CheckBoxes</h1>
    <div id="content">
      <div data-dojo-id="store" data-dojo-type="cbtree/store/ObjectStore" 
        data-dojo-props='url:"../../store/json/Simpsons.json", idProperty:"name"'>
      </div>
      <div data-dojo-id="model" data-dojo-type="cbtree/model/TreeStoreModel" 
        data-dojo-props='store:store, query:{name:"Root"}, rootLabel:"The Family", checkedRoot:true,
        checkedState:true'>
      </div>
      <div data-dojo-id="tree", data-dojo-type="cbtree/Tree" data-dojo-props='model:model, 
        onCheckBoxClick: checkBoxClicked, id:"tree"'>
      </div>
    </div>
    <h3>Click a checkbox</h3>
  </body> 
</html>
```

The other documents in the CheckBox Tree documentation set have additional sample applications.