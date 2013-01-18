<h2 id="checkbox-tree-basics">CheckBox Tree Basics</h2>

The CheckBox Tree is an extension of the standard dijit tree, therefore all
features available with the dijit tree are also offered by the CheckBox Tree
with the exception of dijit Tree V1.0 backward compatibility, that is, the
CheckBox Tree requires a model.  
This document describes the CheckBox Tree extensions only. Details of the
standard dijit tree can be found 
[here](http://dojotoolkit.org/reference-guide/1.7/dijit/Tree.html).

* [CheckBox Tree Properties](#checkbox-tree-properties)
* [CheckBox Tree API](#checkbox-tree-api)
* [CheckBox Tree Placement](#checkbox-tree-placement)
* [Sample Application](#sample-application)

### CheckBoxes & Tree Store Models ###

The CheckBox Tree is implemented using the Model-View-Controller (MVC) pattern
and as a result the tree itself is considered the 'View' or presentation layer.
The tree offers the ability to instantiate a checkbox for tree nodes but does
not control whether or not a tree node is eligible to get a checkbox, that is
actually determined by the model. The CheckBox Tree comes with three [Store Models](StoreModels.md):

* [TreeStoreModel](StoreModels.md#tree-store-model)
* [ForestStoreModel](StoreModels.md#forest-store-model)
* [FileStoreModel](StoreModels.md#file-store-model)

The [model properties](StoreModels.md#store-model-properties) and configuration determine
checkbox eligibility for tree nodes. By default, all models are configured to
generate checkboxes for every tree node. 

If you are planning to write your own model, please refer to '/cbtree/models/model.js'
for the model interface definition.

### CheckBox vs Checked State ###

The model provides the primary interface to get or set the checked state for any data
item by means of the *getChecked()* and *setChecked()* methods. In general, a model
refers to the 'checked' state rather than a checkbox state simply because a checkbox
is just the visual representation of a state. As a case in point, one could simply
replace the default checkbox with a third party widget, using the trees *widget*
property, as long as the widget is capable of representing a 'checked' state.
(See [Mixing in other Widgets](#mixing-in-other-widgets) for an example).

Although you can get or set the checked state via a tree node using *get("checked")* 
or *set("checked", false)*, *checked* is not an actual property of a tree node instead
the request is redirected to the models *getChecked()* and *setChecked()* methods.

### Parent-Child Relationship ###
One of the Store Model features is the ability to maintain a parent-child relationship.
The parent checked state, represented as a tree branch checkbox, is the composite
state of all its child checkboxes. For example, if the child checkboxes are either
all checked or unchecked the parent will get the same checked or unchecked state.
If however, the children checked state is mixed, that is, some are checked while
others are unchecked, the parent will get a so called 'mixed' state.

### CheckBox Tree Styling ###
The Tree Styling extension allows you to dynamically manage the tree styling 
on a per data item basis. Using the simple to use accessor *set()* you can alter
the icon, label and row styling for the entire tree or just a single data item.
For example: *set("iconClass", "myIcon", item)* changes the css icon class associated
with all tree node instances of data item *item*, or if you want to change the
label style:

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

### Store Model API ###

As stated before, the CheckBox Tree comes with an optional [Store Model API](StoreModelAPI.md)
which serves both the TreeStoreModel as well as the ForestStoreModel. The Store
Model API allows the user to programmatically build and maintain checkbox trees.
For example, you can create your store starting with an empty JSON dataset or use
an existing data store and use the API to add, remove or change store items.
In addition, you can simply check/uncheck a single store item or a set of store
items using a store query or manage any store item attributes using the *setItemAttr()*
and *getItemAttr()* methods. Some of the Store Model API methods are:

* check(), uncheck()
* getChecked(), setChecked()
* getItemAttr(), setItemAttr()
* fetchItem(), fetchItemsWithChecked()
* addReference(), removeReference()

For a detailed description of all API methods, please refer to [Store Model API](StoreModelAPI.md). 
Demo application *tree03.html* in the directory '/cbtree/demos' demonstrates all
features of the Store Model API.

<h2 id="checkbox-tree-properties">CheckBox Tree Properties</h2>

In addition to the standard dijit tree properties the CheckBox Tree has the
following set of public properties. For each property the type and default
value is listed. 

#### branchIcons:
> Boolean (true), determines if the FolderOpen/FolderClosed icon or their custom
> equivalent is displayed. If false, the branch icon is hidden.

#### branchReadOnly:
> Boolean (false), determines if branch checkboxes are read only. If true, the
> user must explicitly check/uncheck every child checkbox individually and thus
> overwriting the per store item 'enabled' features for any store item associated
> with a tree branch.

#### checkBoxes:
> Boolean (true), if true it enables the creation of checkboxes, If a tree node
> actually gets a checkbox depends on the configuration of the [model properties](StoreModels.md#store-model-properties)
> If false no checkboxes will be created regardless of the model configuration.

> NOTE: if checkBoxes is true, the model for the tree **MUST** support the 
> getChecked() and setChecked() methods.

#### leafReadOnly:
> Boolean (false), determines if leaf checkboxes are read only. If true, the
> user can only check/uncheck branch checkboxes and thus overwriting the per
> store item 'enabled' features for any store item associated with a tree leaf.

#### nodeIcons:
> Boolean (true), Determines if the Leaf icon, or its custom equivalent, is
> displayed. If false, the node or leaf icon is hidden.

#### widget:
> Object (null), specifies the checkbox widget to be instanciated for the tree
> node. The default is the CheckBox Tree multi-state checkbox. (see [mixin widgets](#mixing-in-other-widgets)
for details).

##### Example
The following example illustrates how to apply the CheckBox Tree properties.
Please note that if the default property value is used the property can be
omitted.

    require(["dojo/data/ItemFileWriteStore", 
             "cbtree/Tree",
             "cbtree/model/TreeStoreModel"], 
      function( ItemFileWriteStore, Tree, TreeStoreModel ) {

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

Additional CheckBox Tree Properties are available when the Tree Styling API is
loaded, see [Tree Styling Properties](TreeStyling.md#styling-properties) for details.

<h2 id="checkbox-tree-api">CheckBox Tree API</h2>
### Accessors ###

As of dojo 1.6 all dijit widgets come with the so called auto-magic accessors
*get()* and *set()*. All CheckBox Tree API's, that is, the CheckBox Tree, Tree
Styling and Store Model API, use these accessors as their primary interface.
For example, to get the checked state of a tree node one could simply call: 
*get("checked")* or to change the checked state call: *set("checked",true)*.

#### Note: ####
The property names *"checked"* and *"enabled"* are automatically mapped to the 
appropriate store item properties based on the store models *checkedAttr* and
*enabledAttr* values. Therefore, at the application level you can simple  use
the keywords *"checked"* and *"enabled"* regardless of the actual store item
properties.
 
*****************************
#### get( propertyName ) ####
> Returns the value of the tree or tree node property identified by *propertyName*.

*propertyName:* String
> Name of the tree or tree node property.

*****************************
#### set( propertyName, newValue ) ####
> Set the value of the tree or tree node property identified by *propertyName*.

*propertyName:* String
> Name of the tree or tree node property.

*value:* AnyType
> New value to be assigned.

### Callbacks ###

*****************************
#### onCheckBoxClick( item, nodeWidget, evt ) ####
> Callback routine called each time a checkbox is clicked.

*item:* data.item
> The data.item associated with the nodeWidget whose checkbox got clicked.

*nodeWidget:* dijit.widget
> The tree node widget.

*evt:* Object
> A dojo event object

##### Example #####

    function checkBoxClicked( item, nodeWidget, evt ) {
      alert( "The new state for " + this.getLabel(item) + " is: " + nodeWidget.get("checked") );
    }
                ...

    var myTree = new Tree( { model: model, id: "MyTree" });

    connect.connect( myTree, "onCheckBoxClick", model, checkBoxClicked );

<h2 id="checkbox-tree-placement">CheckBox Tree Placement</h2>

There are different ways to place the CheckBox Tree in the DOM. The easiest way would be to specify
the parent DOM node when creating the CheckBox Tree. Lets assume we have a &lt;div> defined as follows:

    <div id="CheckBoxTree" style="width:300px; height:100%; border-style:solid; border-width:medium;">
    </div>

Notice the &lt;div> has a width, height and border properties specified. The first
option is to create the CheckBox Tree and specify "CheckBoxTree" as the parent DOM
node like:

    var myTree = new cbtree( { ... }, "CheckBoxTree" };
    myTree.startup();
  
The above method however, replaces the existing DOM node with a new one resulting in 
the loss of all the style properties we previously defined. Alternatively you can
insert your tree as a child node of CheckBoxTree, preserving all of its properties,
as follows:

    var myTree = new cbtree( { ... } };
    myTree.placeAt( "CheckBoxTree" );
    myTree.startup();

You can also specify the location of the child node as a second parameter to the 
*placeAt()* method like:

    myTree.placeAt( "CheckBoxTree", "last" );

The location is relative to the other child nodes of the parent node. The placeAt()
location parameter accepts: *"after"*, *"before"*, *"replace"*, *"only"*, *"first"*
and *"last"*. The default location is *"last"*.
See the place() method of dojo/dom-construct for more details.

<h2 id="checkbox-tree-advanced">CheckBox Tree Advanced</h2>

### Mapping Model Events ###

The CheckBox Tree is completely event driven and after instantiation only acts
upon events generated by the model by mapping those types of events to tree node
properties such as 'label' or 'checked'. Any event that cannot be mapped to a 
tree node property is ignored by the tree. For example, if a data item has a 
property called 'age' and when the property is updated, the model will generate
an event accordingly however, the tree does not known how the 'age' property of
the data item relates to any of the tree node properties and therefore ignores
the event.

However, the CheckBox Tree offers an additional method to map item update events,
generated by the model, to tree node properties.

**********************************************************
#### mapEventToAttr( oldAttr, attr, nodeAttr, value ) ####
> Map an attribute name passed from the model to a tree node property. If the
> optional *value* argument is specified, its value is assigned to the tree node
> property. If *value* is a function, the result returned by the function is
> assigned to the tree node property.

*oldAttr:* String
> Attribute name that is currently mapped to an tree node attribute. If present
> its entry in the table is removed.

*attr:* String
> Attribute name to be mapped. This is typically an attribute/property name of
> a data item.

*nodeAttr:* String
> Attribute/property name of a tree node to which argument *attr* is mapped. Any
> mapping event results in a call to `treeNode.set(nodeAttr, value)`

*value:* AnyType
> Optional, Fixed value to be assigned to *nodeAttr*. However, if value is a function,
> the function is called as `value(item, nodeAttr, modelValue)` and the result
> returned is assigned to *nodeAttr* instead. 

##### Example #####
The following example maps the *age* property of a data.item to the tree node 
property *label* resulting in a new label text each time a checkbox is clicked. 

    <script type="text/javascript">
      require([
        "dojo/_base/connect",
        "dojo/data/ItemFileWriteStore",
        "dojo/ready",
        "cbtree/Tree",
        "cbtree/models/ForestStoreModel"
        ], function( connect, ItemFileWriteStore, ready, Tree, ForestStoreModel ) {
              var store, model, tree;
              
              function makeLabel(item, attr, value ) {
                // summary:
                //    Action routine called when the tree recieved an update event from the model
                //    indicating the 'age' attribute of a store item has changed.
                var labelText = this.getLabel(item) + ' is now ' + value + ' years old';
                return labelText;
              }
              
              function checkBoxClicked( item, nodeWidget, evt ) {
                // summary:
                //    Action routine called when a checkbox is clicked.
                var age = this.store.getValue(item,'age');
                this.store.setValue(item,'age',++age);
              }

              // Declare a simple JSON data set and create the model and tree.
              var dataSet = { identifier:'name', label:'name', items:[
                                {name:'Homer', age:'45'},
                                {name:'Marge', age:'40'}
                              ]};
              store = new ItemFileWriteStore({data: dataSet});
              model = new ForestStoreModel( {store: store, rootLabel:'The Family'});

              ready( function() {
                tree  = new Tree( {model: model, id:'MyTree'}, "CheckboxTree" );
                connect.connect( tree, "onCheckBoxClick", model, checkBoxClicked );

                // Map an 'age' update event to the tree node 'label' propery and call the
                // action routine makeLabel() to get the label text.

                tree.mapEventToAttr( null,'age','label', makeLabel );
                tree.startup();
              });
           }
      );
    </script>

In the above example the following sequence of events take place:

1.   Each time a checkbox is clicked the function *checkBoxClicked()* is called
  and the *age* property of *item* is fetched and incremented followed by a
  store update.
2.  As a result of the store update in step 1 the model will generate an *age*
  property update event.
3.  When the CheckBox Tree recieves the update event it maps the *age* property
  to the tree node property *label* and calls the function *makeLabel()* to get
  the new label text.
4.  Finally, the CheckBox Tree calls *set("label", newLabelText)* for each tree
  node associated with the data item.

Note: The above example does not have the Store Model API loaded otherwise the *checkBoxClicked()*
function could be written as:

    function checkBoxClicked( item, nodeWidget, evt ) {
      // summary:
      //    Action routine called when a checkbox is checked.
      var age = this.getItemAttr(item, 'age');
      this.setItemAttr(item,'age',++age);
    }


<h3 id="mixing-in-other-widgets">Mixing in other Widgets</h3>

By default the CheckBox Tree uses its own multi state checkbox widget to represent
a data items checked state. However, the CheckBox Tree also allows you to mixin
other widgets which are capable of representing a checked state.

The optional CheckBox Tree property *widget* is an object with the following
properties:

#### type: ####
> Widget  | String. The widget must support the accessors *get()* and *set()*
> and must have the *checked* property. In addition, the widgets should not stop any 
> 'onClick' events calling *event.stop()* nor should it change any related
> widget instances without generating an onClick event for those widgets.
> If *type* is a string, the string value must be a module ID. For example 
> "dojox/form/TriStateCheckbox"

#### args: ####
> Object, Optional list of arguments as a set of JavaScript 'property name: value'
> pairs to be passed to the constructor of the widget.

#### target: ####
> String, optional name of the target DOM node of a click event, The default is 'INPUT'.

#### mixin: ####
> Function, optional function called prior to instantiation of the widget. If
> specified, called as: *mixin( args )* were *args* is the list of arguments to
> be passed to the widget constructor. Argument *args* is the widget property
> *args* with the default arguments required by the CheckBox Tree mixed in. In
> the function body the "this" object equates to the enclosing node widget.

#### postCreate: ####
> Function, optional function called immediately after the creation of the widget.
> If specified, called as: *postCreate()*. In the function body the "this" object
> equates to the newly created widget.

The following sample is the core of demo application '/cbtree/demos/tree05.html'.
In this example the ToggleButton widget is used instead of the default CheckBox
Tree multi-state checkbox. Note: this sample also relies on the Tree Styling API
being loaded to hide the tree node labels and icons.

    <script type="text/javascript">
      require([
        "dojo/ready",
        "dojo/data/ItemFileWriteStore",
        "dijit/form/ToggleButton",
        "cbtree/Tree",                      // CheckBox Tree              
        "cbtree/TreeStyling",               // Tree Styling API
        "cbtree/models/ForestStoreModel"    // Tree Forest Store Model
        ], function( ready, ItemFileWriteStore, ToggleButton, Tree, TreeStyling, ForestStoreModel ) {

          var store = new ItemFileWriteStore( { url: "../datastore/Simpsons.json" });
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


<h2 id="sample-application">Sample Application</h2>

The following is a basic sample of how to create a model, associate the model
with the tree and display the tree itself. In addition, the sample application
connects the *onCheckBoxClick* event of the tree and as a result every time
a checkbox is clicked the function *checkBoxClicked()* is called.

    <!DOCTYPE html>
    <html>
      <head> 
        <meta charset="utf-8">
        <title>Dijit Tree with Checkboxes</title>     
        <style type="text/css">
          @import "../../dijit/themes/claro/claro.css";
          @import "../themes/claro/claro.css";
        </style>

        <script type="text/javascript">
          var dojoConfig = {
                async: true,
                parseOnLoad: true,
                isDebug: false,
                baseUrl: "../../",
                packages: [
                  { name: "dojo",  location: "dojo" },
                  { name: "dijit", location: "dijit" },
                  { name: "cbtree",location: "cbtree" }
                ]
          };
        </script>

        <script type="text/javascript" src="../../dojo/dojo.js"></script> 
        <script type="text/javascript">
          require([
            "dojo/_base/connect",
            "dojo/data/ItemFileWriteStore",
            "dojo/ready",
            "cbtree/Tree",                    // Checkbox tree
            "cbtree/models/ForestStoreModel"  // ForestStoreModel
            ], function( connect, ItemFileWriteStore, ready, Tree, ForestStoreModel) {

              var store, model, tree;
              
              function checkBoxClicked( item, nodeWidget, evt ) {
                alert( "The new state for " + this.getLabel(item) + " is: " + nodeWidget.get("checked") );
              }

              store = new ItemFileWriteStore( { url: "../datastore/Family-1.7.json" });
              model = new ForestStoreModel( {
                      store: store,
                      query: {type: 'parent'},
                      rootLabel: 'The Family',
                      checkedRoot: true,
                      checkedState: true
                      }); 

              ready(function() {
                tree = new Tree( {
                        model: model,
                        id: "MenuTree",
                        branchReadOnly: false,
                        branchIcons: true,
                        nodeIcons: true
                        }, "CheckboxTree" );
                tree.startup();
                connect.connect( tree, "onCheckBoxClick", model, checkBoxClicked );
              });
            });
        </script>
      </head>
        
      <body class="claro">
        <h1 class="DemoTitle">Dijit Tree with Multi State CheckBoxes</h1>
        <div id="CheckboxTree">  
        </div>
        <h2>Click a checkbox</h2>
      </body> 
    </html>

Following is the same program but this time we instanciate all elements declarative
using the HTML5 custom  *data-dojo*-xxx attributes. Notice that because we take the
declarative route we must explicitly include *dojo/parser* to parse our document.
The dojo parser will process all *data-dojo*-xxx attributes, without the parser loaded
you will see nothing on your screen.

    <!DOCTYPE html>
    <html>
      <head> 
        <meta charset="utf-8">
        <title>Dijit Tree with Checkboxes</title>     
        <style type="text/css">
          @import "../../dijit/themes/claro/claro.css";
          @import "../themes/claro/claro.css";
        </style>

        <script type="text/javascript">
          var dojoConfig = {
            async: true,
            parseOnLoad: true,
            isDebug: false,
            baseUrl: "../../",
            packages: [
              { name: "dojo",  location: "dojo" },
              { name: "dijit", location: "dijit" },
              { name: "cbtree",location: "cbtree" }
            ]
          };
        </script>

        <script type="text/javascript" src="../../dojo/dojo.js"></script> 
        <script type="text/javascript">
          require([
            "dojo/data/ItemFileWriteStore",
            "dojo/parser",                    // dojo parser
            "cbtree/Tree",                    // Checkbox tree
            "cbtree/models/ForestStoreModel"  // ForestStoreModel
          ]);

          function checkBoxClicked( item, nodeWidget, evt ) {
            alert( "The new state for " + this.getLabel(item) + " is: " + nodeWidget.get("checked") );
          }
        </script>
      </head>
      
      <body class="claro">
        <h1 class="DemoTitle">Dijit Tree with Multi State CheckBoxes</h1>
        <div id="content">
          <div data-dojo-id="store" data-dojo-type="dojo/data/ItemFileWriteStore" 
            data-dojo-props='url:"../datastore/Family-1.7.json"'>
          </div>
          <div data-dojo-id="model" data-dojo-type="cbtree/models/ForestStoreModel" 
            data-dojo-props='store:store, query:{type:"parent"}, rootLabel:"The Family", checkedRoot:true,
            checkedState:true'>
          </div>
          <div data-dojo-id="tree", data-dojo-type="cbtree/Tree" data-dojo-props='model:model, 
            onCheckBoxClick: checkBoxClicked, id:"tree"'>
          </div>
        </div>
        <h2>Click a checkbox</h2>
      </body> 
    </html>


The other documents in the CheckBox Tree documentation set have additional sample applications.