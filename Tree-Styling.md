
The CheckBox Tree comes with a simple to use Styling extension which is loaded
as a separate module. The Styling extension allows you to dynamically manage
tree node icons, labels and row styling either for the entire tree or on a per
item basis. 

The Tree Styling extension is not limited to the CheckBox Tree but can also
be used with the default `dijit/Tree` tree.

### Content
* [Loading the Styling Extension](#loading-the-styling-extension)
* [Styling Properties](#styling-properties)
* [Styling API](#the-styling-api)
* [Adding Custom Icons](#adding-custom-icons)
* [Item Property Mapping](#item-property-mapping)
* [Sample Applications](#sample-application)

<h2 id="loading-the-styling-extension">Loading the Styling Extension</h2>
Tree Styling is implemented as an extension to the CheckBox Tree and as such
must to be loaded as a separate module. The following sample shows how to load
the Styling extension. The Style extension is located at **cbtree/extensions/TreeStyling**

```javascript
require(["cbtree/store/Hierarchy", 
         "cbtree/Tree",
         "cbtree/model/TreeStoreModel", 
         "cbtree/extensions/TreeStyling"]  // Load Tree Styling extension
  function( Hierarchy, Tree, TreeStoreModel, TreeStyling ) {
                ...
  }
);
```
If you instantiate your tree declarative you **MUST** load the Styling extension
programmatically, this because it is an extension and has no constructor.

```javascript
require(["dojo/parser",                  // dojo parser
         "cbtree/Tree",                  // CheckBox Tree
         "cbtree/extensions/TreeStyling" // Tree styling extensions
        ]);
```

Also, see the second [example](#sample-application) below.

<h2 id="styling-properties">Styling Properties</h2>
### Tree Properties
Whenever the Styling extension is loaded the CheckBox Tree gets two more public
properties:

#### icon:
> **_TYPE:_** String | Object

> Set the default icon or set of icons for the CheckBox Tree.
> see [icon properties](#icon-properties) for more details.

> **_DEFAULT:_** null

#### valueToIconMap:
> **_TYPE:_** Object

> Maps item property values to icons. The tree node icons can be set based on 
> the value of an item property. The object is defined using the following ABNF
> notation:
>
><pre>valueToIconMap = "{" property-map *("," property-map) "}"
property-map   = property ":" mapping
mapping        = string-array / keyval-object
string-array   = "[" [string] *("," string) "]"
keyval-object  = "{" [keyval] *("," keyval) "}"
keyval         = key ":" value
key            = property / wildcard
value          = string / icon-object / wildcard / "null"
property       = js-identifier / DQUOTE js-identifier DQUOTE
string         = DQUOTE *CHAR DQUOTE
wildcard       = DQUOTE "*" DQUOTE
</pre>
> Please see [Item Property Mapping](#item-property-mapping) for additional information
> and examples.

> **_DEFAULT:_** null

### Node Properties
Styling can be applied to tree node icons, labels and rows either at the common 
tree level or on a per item basis. Each of those tree node elements has two 
styling catagories: **_Class_** and **_Style_**.

The property names associated with the styling catagories are the catagory name
prefixed with the element name **_icon_**, **_label_** or **_row_**. As a result
the following property names are available:

- iconClass & iconStyle
- labelClass & labelStyle
- rowClass & rowStyle

The **_Class_** properties are all of type String, whereas all **_Style_** properies
are JavaScript key:value pairs objects suitable for the input to `domStyle.set()`
A style property looks like:

```javascript
{color:"red", border:"solid"}
```
<span class="octicon octicon-alert"></span>`domStyle.set()` objects
requires camelcase property names, therefore a css property like `border-width`
is specified as `borderWidth`.




<h2 id="the-styling-api">The Styling API</h2>
The Styling extension extends the Tree API with two simple to use accessors to
either get or set styling properties of the tree, or tree nodes on a per item
basis.
In the context of this API, an item can represent almost anything and depends
on the type of model that is associated with the tree.
If for example the model is one of the store models than an item represents an
object in a `cbtree/store`.

********************************************
#### get( propertyName, item? )

**_propertyName_**: String  
> The name of the styling property.  

**_item_**: Object?
> A JavaScript key:value pairs object. if the item argument is omitted the 
> property of the tree is retrieved.

**returns**: String | Object
> Returns a string if the property refers to a 'Class' property otherwise an 
> object.

********************************************
#### set( propertyName, newValue, item? )

**_propertyName_**: String  
> The name of the styling property.  

**_newValue_**: String | Object  
> The new value to be assigned to the property.  

**_item_**: Object?
> A JavaScript key:value pairs object, If the item argument is omitted the 
> property of the tree is set.

**returns**: String | Object
> Returns argument *newValue*  

### Styling API Usage
The following are several examples of the Tree API extensions and Styling properties:

```javascript
get("iconClass", item)
get("labelStyle", item)
get("labelStyle")

set("labelStyle", {color:"red"}, item)
set("labelStyle", {color:"red"})
set("icon", {iconClass:"myIcons", indent: false}, item);
set("icon", "myIcons")
```



<h2 id="adding-custom-icons">Adding Custom Icons</h2>
The Tree Styling extension provides different methods to add custom icons to your
tree. These methods are:

1. Using the tree **_icon_** property in which case you set the default 
	icons for the entire tree,
2. Using the accessor `set()` allowing you to set the icons for	the entire tree
	or for individual data items,
3. As a property of a data item or,
4. By item property value mapping.

In order to add custom icons to the CheckBox or dijit Tree the following items
are required:

1. An image sprite containing at least three icons which are referenced as:
  
   1. Terminal
   2. Collapsed
   3. Expanded
<br /><br />

2.  A css file defining the icon classes. If you want to create multiple custom
    icon sets it is recommended to create a 'master' css file which imports the
    individual css files. As an example: `cbtree/icons/cbtreeIcons.css` is the
    default master css file.
      
3.  A link in your HTML file to the master css file, if you have one, otherwise
		a link to your dedicated css file. For example:

    `<link rel="stylesheet" href="../cbtree/icons/myIcons.css" />`

Alternatively you could import your css file in one of the theme related css file
used by cbtree. For example: claro.css, tundra.css located in one of the themes
related directories.

As an example, the picture below is a single image file containing three icons,
each icon is 16 pixels wide. The icon on the left at offset -0 is referred to as
the *Terminal* icon, the icon in the middle at offset -16 is the *Collapsed* 
icon and the icon at the right at offset -32 is the *Expanded* icon.

<img src="images/CustomIcons.png" alt="sprite"></img>

<h3 id="icon-object">Icon Object</h3>
The CheckBox Tree and Styling extension functions accept icons either as a 
string argument or as an icon object. An icon object is JavaScript key:value
pairs objects with the following ABNF notation:

```
icon-object  = "{" icon-class *("," icon-prop) "}"
icon-class   = "iconClass" ":" class-string
icon-prop    = icon-style / fixed / indent
icon-style   = "iconStyle" ":" style-object
fixed        = "fixed" ":" css-class
indent       = "indent" ":" (boolean / 1*DIGIT)
class-string = DQUOTE base-class *(WSP css-class) DQUOTE
base-class   = css-class
style-object = "{" style-prop *("," style-prop) "}"
style-prop   = property ":" json-value
property     = js-identifier / DQUOTE js-identifier DQUOTE
boolean      = "true" / "false"
```

<h3 id="icon-properties">Icon Properties</h3>

<h4 id="iconclass">iconClass</h4>
> **_TYPE:_** String

> Required, the *iconClass* property identifies the css classes of the icon. 
> The class string is a space separated list of css class names.
> If multiple class names are specified the first in the list is used as the
> icons base class (see indent)

#### iconStyle
> **_TYPE:_** Object

> Optional, the *iconStyle* property specifies the css style of the icon. The
> iconStyle is an object suitable for the input to domStyle.set() like {border:"solid"}
> <span class="octicon octicon-alert"></span>`domStyle.set()` objects
requires camelcase property names, therefore a css property like `border-width`
is specified as `borderWidth`.

#### fixed
> **_TYPE:_** String

> Optional, an additional css class name. If set, the *indent* property is
> ignored and the class names for the icon are always the icon properties:
> *iconClass* + *fixed* regardless of the indent level of the tree node.
  
#### indent 
> **_TYPE:_** Boolean | Number

> Optional, specifying if an additional class name is
> generated depending on the indent level of a tree node. If *true* (default),
> the additional class name is generated using following ABNF notation: 

><pre>
className   = baseClass ("Expanded" / "Collapsed" / "Terminal") "_" indentLevel  
baseClass   = js-identifier
indentLevel = Number
</pre>

If the *indent* property is specified as an integer its value signifies the maximum
indent depth for which the additional class name is generated. For any indent depth
greater than the indent value no additional class name is generated.

##### Examples

```javascript
icon = { iconClass:"myIcons", iconStyle:{border:"solid"}, fixed:"myIconNode", indent:false};
icon = { iconClass:"myIcons", indent: 3 };
icon = { iconClass:"myIcons" }
icon = "myIcons"
```

If the icon is a string argument, as in the last example, the Tree Styling API will
automatically convert it to an icon object like:  
`{ iconClass:"myIcons", indent: true}`

### The CSS file content. ###
    
Assuming you are creating the css file for the css class name 'myIcons' and each of the
icon bitmaps is 16 pixels wide, the basic (minimal) css file MUST look like:

```css
.myIcons {
  background-image: url( "images/myIcons.gif" );
  background-repeat: no-repeat;
}

.myIcons.myIconsTerminal {
  background-position: -0px;
}

.myIcons.myIconsCollapsed {
  background-position: -16px;
}

.myIcons.myIconsExpanded {
  background-position: -32px;
}
```

*NOTE:* There is **NO** white space between the css class names.

### Creating the Checkbox Tree ###
Now that we have created our icon image file (sprite) and the associated css
file we can create the tree and set our custom icon as the default for the tree.
The default tree icon is set using the trees **_icon_** property:


```javascript
icon: { iconClass: "myIcons", indent: false },
```

##### Example

```html
<link rel="stylesheet" href="../cbtree/icons/myIcons.css" />
                  ...
<script type="text/javascript">
  require(["cbtree/store/Hierarchy", 
           "cbtree/Tree",
           "cbtree/extensions/TreeStyling",
           "cbtree/model/TreeStoreModel"
          ], function( Hierarchy, Tree, TreeStyling, TreeStoreModel ) {

      var store = new Hierarchy( { url: "myFamilyTree.json" });
      var model = new TreeStoreModel( { store: store, ... });
      var tree  = new Tree( {
              model: model,
              id: "MenuTree",
              icon: { iconClass: "myIcons", indent: false },
                  ...
              });
</script>
```
<span class="octicon octicon-alert"></span>
Make sure you first load the css file (line 1) otherwise the icons will not show in your
browser.

### Icon as an item property ###

In addition to setting the trees **_icon_** property or using the `set()`
function you can also set the [iconAttr](Model-API#wiki-iconattr) property of
the store model.
The model **_iconAttr_** property identifies the property name of a data item as
being either an icon object or icon class name.

For example, if you create a JSON store item like:
```json
{ "name":"Lisa", "checked":true, "icon":"myIcon" }
```
You could create your model as follows:

```javascript
var model = new TreeStoreModel( {
                    store: store,
                    query: {type: "parent"},
                    rootLabel: "The Family",
                    iconAttr:"icon",
                         ...
                  }); 
```

In the above example the store model is told that the **_icon_** property of a 
data item needs to be handled as the icon class. 
Also, any updates to the specified property of the data item will be treated by
the tree as an update to the item's icon. 
See also [Item Property Mapping](#item-property-mapping)

### Multi Level Icons ###
    
In addition to the basic configuration described above, the CheckBox Tree
also allows for the use of different icons depending on the tree node indent
level. Whenever the icon property **_indent_** is set, the CheckBox Tree adds an
additional css class for each icon which is the iconClass with the current
indent level appended. For example: assuming your icon class is still 
'myIcons' and an expanded icon is located at indent level 1 the following
css classes are set for the iconNode:

1. myIcons 
2. myIconsExpanded
3. myIconsExpanded_1

To use multi level icons add at least one more icon to your sprite. The icon on 
the right is now at offset -48.

<img src="images/CustomIcons2.png" alt="sprite"></img>

Add the following to your css file.
```css
.myIcons.myIconsTerminal_1,
.myIcons.myIconsCollapsed_1,
.myIcons.myIconsExpanded_1 {
  background-position: -48px;
}
```
<span class="octicon octicon-alert"></span>
Notice though that all class names map to the same icon, the one on the right.
To get the full user experiance you would need to add an icon for each icon
class and define the css classes accordingly.

Now create the tree with the icon property **_ident_** set to true (default):
  
```javascript
var myTree = new Tree( {
        model: model,
        id: "MenuTree",
        icon: { iconClass: "myIcons", indent: true },
            ...
        });
```

or, set it dynamically after tree creation:

```javascript
myTree.set("icon", { iconClass: "myIcons" })

        or simply:

myTree.set("icon", "myIcons" })
```

Demo application `cbtree/demos/store/tree09.html` demonstrates the implementation 
and use of multi level icons.


<h2 id="item-property-mapping">Item Property Mapping</h2>
Tree node icons can also be assigned based on the value of item properties. The
tree property **_valueToIconMap_** defines the mapping of item properties to
icons. The following is a description of the different methods to map item
property to icons.

#### Property value as iconClass
The simplest method is to use the item property value as the [iconClass](#iconclass)
 by mapping the property name to an empty object. The following example maps the 
 item property **_type_** to an empty object, both assignments are functionally
 identical:
```javascript
var valueToIconMap = { type: {} };
			or
var valueToIconMap = { type: [] };
```
In this case the value of the property item **_type_** is used as the iconClass.
For example, if the value of type is *StreetAddress* the iconClass will also be
*StreetAddress*.

#### Using specific values only
Instead of simply using any item property value as the iconClass you can also 
specify specific property value. For example, if you want to map only certain
property values consider the following syntax:

```javascript
{ type: [ "POI", "StreetAddress", "City", "Store" ] };
			or
{ type: { "POI": null, "StreetAddress": null, "City": null, "Store": null };
```
In this case only the property values *POI*, *StreetAddress*, *City* and *Store*
are used as the iconClass. Any other property value will be ignored and therefore
have no effect on the tree node icon. See also  *Using wildcards*.

#### Mapping value to iconClass
The third method is to map item property values to specific icon class names or
icon objects. First we map specific property values to distinct iconClass names:

```javascript
{ type: { "POI": "pointOfInterest", "StreetAddress":"poiAddress"} }
```
If the item property **_type_** has a value of *POI* the icon class name for the
tree node is set to *pointOfInterest* or if the value is *StreetAddress* the
icon class name is set to *poiAddress*.

Next we map a property value to a specific icon  object:

```javascript
{ type: { "POI": {iconClass: "pointOfInterest", iconStyle: {border:"solid"}, indent: false} } }
```

#### Mapping value to a Function
The last method is to map a property value to a function. The function is called
with three arguments as in `function (item, property, value)`. The function must
either return a valid string of css class names or `null`.
```javascript
{ type: { "POI": function (item, property, value) {
                           ...
                   return cssClassname;
                 }
        }
```

#### Using a wildcard
Sometimes it is not feasable to map every possible value of an item property or
you just want to map a subset of the values and have other values default to a
standard icon. To do this you can use the wildcard character (*) as shown below:

```javascript
{ type: { "POI": "pointOfInterest", "StreetAddress":"streetAddress", "*":"defaultIcon"} }
```
In this case the destinct **_type_** property values *POI* and *StreetAddress*
are mapped to specific iconClass names while any other value will be mapped
to the iconClass *defaultIcon*. If you omit the wildcard any value not mapped
will default to either:

1. The specific icon set for the item.
2. The default icon set for the tree.
3. The default dijit icons.

##### Wildcard as a Placeholder.
The wildacard character (*) can be used as a placeholder for property value
injection. Lets assume that in addition to the property value itself you also 
want to add an additional base class to your icon:

```javascript
{ type: { "*": "makiIcon *" } }
```
Now, if we have an item with the type property value of 'CityStreet', than the
above mapping will take the items *type* property value and creates the css
class `makiIcon CityStreet`. Simply speaking, the wildcard character (*) is
replaced with the property value.

<span class="mega-octicon octicon-alert"></span>
Icons mapped using the trees *valueToIconMap* property will **_NOT_** trigger
an automatic tree update if the value of the associated property is changed in
the store unless you also set the [iconAttr](Model-API#wiki-iconattr) property
of the model.

### Mapping Multiple Properties
The tree property **_valueToIconMap_** allows you to map multiple item properties
to further fine tune the icon class that will be assigned. Whenever multiple
properties are defined a logical OR is performed on the properties. Consider the 
following example:

```javascript
var valueToIconMap = { type: ["POI", "StreetAddress"], 
                       class: ["map"],
                       id: { "*":"defaultIcon" } 
                     };
                       
```
If an item has a property *type* with a value of either *POI* or *StreetAddress* 
then the *type* value is used as the icon class. Otherwise, if the item has a 
property *class* with the value *map* then *map* is used as the icon class.
Otherwise, if the item  has a property *id* then the icon class will be set to 
*defaultIcon*.

<h3 id="nested-item-properties">Nested Item Properties</h3>
If an item property value is an object or a nested object you can specify the
property name you want to map as a dot (.) separated path enclosed in double
quotes. 
If, for example, you would like to map the *type* property of the following
object:
```javascript
{ name: "New York", feature: { attributes: { type:"city", harbor: true }, ... }}
```
The *valueToIconMap* object would look something like:
```javascript
var valueToIconMap = { "feature.attributes.type": {"city":"poiCity"} };
```


<h2 id="icon-assignment-order">Icon Assignment Order</h2>
The css class name(s) assigned to tree nodes is determined in the following order

1. By mapping an item property value, if the tree *valueToIconMap* property is
set and a mapping can be made, otherwise use:
2. The specific icon set for a given item, otherwise use:
3. The default icon set for the tree, otherwise use:
4. The default dijit tree icon classes.

<span class="mega-octicon octicon-alert"></span>
Regardless of how the css class name(s) for a tree node are determined it is
paramount you have the appropriate css class name definitions loaded along with
the dojo, dijit and cbtree modules. If any css class name definition is missing
the intended icon will not show in your browser.

<h2 id="sample-application">Sample Application</h2>

The first sample application demonstrates some of the Tree Styling API features.

```html
<!DOCTYPE html>
<html>
  <head> 
    <meta charset="utf-8">
    <title>The CheckBox Tree</title>     
    <style type="text/css">
      @import "../../../dijit/themes/claro/claro.css";
      @import "../../themes/claro/claro.css";
    </style>

    <script type='text/javascript'>
      var dojoConfig = { async: true, parseOnLoad: true,  isDebug: false,
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
      require(["dojo/ready",
               "cbtree/Tree",                    // Checkbox tree
               "cbtree/extensions/TreeStyling",  // Tree styling extensions
               "cbtree/model/TreeStoreModel",    // TreeStoreModel
               "cbtree/store/Hierarchy",
              ], function( ready, Tree, TreeStyling, TreeStoreModel, Hierarchy ) {

          function checkBoxClicked( item, nodeWidget, evt ) {
            var newState = nodeWidget.get("checked" );
            var label    = this.getLabel(item);
            
            if( newState ) {
              this.set("iconStyle", {border:"solid"}, item );
              this.set("labelStyle",{color:"red"}, item );
            } else {
              this.set("iconStyle", {border:"none"}, item );
              this.set("labelStyle",{color:"black"}, item );
            }
            alert( "The new state for " + label + " is: " + newState );
          }

          var store = new Hierarchy( { url: "../../store/json/Family.json", idProperty:"name" });
          var model = new TreeStoreModel( {
                              store: store,
                              query: {name: "Root"},
                              rootLabel: "The Family",
                              checkedRoot: true
                            }); 

          ready(function() {
            var tree = new Tree( { model: model, id: "MenuTree"  }, "CheckboxTree" );
            tree.on( "checkBoxClick", checkBoxClicked );
            tree.startup();
          });
        });
    </script>
  </head>
  <body class="claro">
    <h1 class="DemoTitle">CheckBox Tree Styling</h1>
    <div id="CheckboxTree">  
    </div>
    <h2>Click a checkbox</h2>
  </body> 
</html>
```

The following is the same sample application but this time we instantiate the
store, model and tree declarative:

```html
<!DOCTYPE html>
<html>
  <head> 
    <meta charset="utf-8">
    <title>Dijit Tree with Checkboxes</title>     
    <style type="text/css">
      @import "../../../dijit/themes/claro/claro.css";
      @import "../../themes/claro/claro.css";
    </style>

    <script type="text/javascript">
      var dojoConfig = { async: true, parseOnLoad: true,  isDebug: false,
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
      // Load dependencies...
      require(["dojo/parser",                    // dojo parser
               "dojo/on",
               "cbtree/Tree",                    // CheckBox Tree
               "cbtree/extensions/TreeStyling",  // Tree styling extensions
               "cbtree/model/TreeStoreModel",    // TreeStoreModel
               "cbtree/store/Hierarchy"
              ]);
    </script>
  </head>
  <body class="claro">
    <h1 class="DemoTitle">CheckBox Tree Styling</h1>
    <div id="content">
      <div data-dojo-id="store" data-dojo-type="cbtree/store/Hierarchy" 
       data-dojo-props='url:"../../store/json/Family.json", idProperty:"name"'>
      </div>
      <div data-dojo-id="model" data-dojo-type="cbtree/model/TreeStoreModel"
        data-dojo-props='store:store, query:{name:"Root"}, rootLabel:"The Family"'>
      </div>
      <div data-dojo-id="tree", data-dojo-type="cbtree/Tree" data-dojo-props='model:model, id:"tree"'>
        <script type="dojo/on" data-dojo-event="checkBoxClick" data-dojo-args="item, nodeWidget, event">
          var newState = nodeWidget.get("checked" );
          var label    = this.getLabel(item);
          if( newState ) {
            this.set("iconStyle", {border:"solid"}, item );
            this.set("labelStyle",{color:"red"}, item );
          } else {
            this.set("iconStyle", {border:"none"}, item );
            this.set("labelStyle",{color:"black"}, item );
          }
          alert( "The new state for " + label + " is: " + newState );
        </script>
      </div>
    </div>
    <h2>Click a checkbox</h2>
  </body> 
</html>
```

The CheckBox tree comes with several examples of custom icons and their associated
css files. These examples can be found at **cbtree/icons** and **cbtree/icons/images**.
