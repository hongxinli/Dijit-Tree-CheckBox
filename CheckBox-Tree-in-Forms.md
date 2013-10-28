The **_TreeOnSubmit_** extension `cbtree/extension/TreeOnSubmit` aids in submitting
HTML forms that include a Checkbox Tree. This extension is optional and only required when:

1.	Submitting forms that include a Checkbox Tree and,
2.	The Checkbox Tree property [_attachToForm_](CheckBox-Tree-API#wiki-attachtoform) is
	specified as an object.

<h3>Content <span class="mega-octicon octicon-book"></span></h3>
* [Introduction](#introduction)
* [The TreeOnSubmit Extension](#the-treeonsubmit-extension)
* [Loading the Extension](#loading-the-extension)
* [Use Custom Extension](#use-custom-extension)
* [Sample Applications](#sample-application)

<h2 id="introduction">Introduction</h2>

A Checkbox Tree in a HTML form needs some special consideration when submitting the form.
Each HTML form is associated with a [_form data set_](http://www.w3.org/TR/html4/interact/forms.html#form-data-set)
which is a set of [_Successful Controls_](http://www.w3.org/TR/html4/interact/forms.html#h-17.13.2).
For a control (checkbox) to be successful it must, at a minimum, comply with the following
requirements:

1. The control **_MUST_** be included in the form (must have a DOM node).
2. The control **_MUST_** have a name and value attribute<a href="#controlvalue"><sup>[1]</sup></a>
3. The control **_MUST_** be "on", that is, checked.
4. The control **_MUST_** be enabled.

Any Checkbox Tree checkbox compliant with the aforementioned requirements is considered
[eligible](CheckBox-Tree-Usage#wiki-eligible-form-checkboxes) for form submission.

<sup id="controlvalue">[1]</sup> If a control doesn't have a current value when the form
is submitted, user agents are not required to treat it as a successful controls.

### Force checkbox inclusion

When a tree branch checkbox is checked, all of its children also get checked, but if the
branch is collapsed the children will not be included in the form, simply because the
children have no tree node in the DOM.
The Checkbox Tree offers a number of controls to force tree nodes to be included in a
form, all of which are related to expanding the tree making child nodes visible:

<table>
	<tr>
		<th>property</th>
		<th>default</th>
		<th>description</th>
	</tr>

	<tr>
		<td>autoExpand</td>
		<td>false</td>
		<td>
			Fully expand the tree on load.
		</td>
	</tr>
	<tr>
		<td>closeOnUnchecked</td>
		<td>false</td>
		<td>
			If true, unchecking a branch node checkbox will close/collapse the branch.
			In addition, when all children of a given branch are unchecked the branch
			will also collapse, that is, if the model property <a href="Model-API#wiki-checkedstrict">checkedStrict</a>
			is enabled.
		</td>
	</tr>
	<tr>
		<td>openOnChecked</td>
		<td>false</td>
		<td>
			If true, checking a branch checkbox will open/expand the branch.
		</td>
	</tr>
</table>

The downside to the relying on any of the above tree properties is that they do not account
for the user clicking the expando icon on branches. If a user clicks the +/- icon, located
in front of a tree branch, the branch will open or close regardless of the settings of any
of the above properties. Second, you may not want to expand the tree, especially if the
tree is large and many levels deep.

### The Checkbox Name and Value Attributes

To understand how the Checkbox Tree handles both the _name_ and _value_ attributes of
HTML &lt;input> elements of type checkbox please refer to the usage section
[Checkboxes in HTML forms](CheckBox-Tree-Usage#wiki-checkboxes-in-html-forms)





<h2 id="the-treeonsubmit-extension">The TreeOnSubmit Extension</h2>

When loading the TreeOnSubmit extension, the default Checkbox Tree event listener `onSubmit()`
is replaced with one that, when triggered, collects the checked states of some or all store
objects. The result is a JSON encoded array of key:value pairs objects which is assigned
to a **_single_** hidden field in the form. The name of the hidden field is configurable
using the tree property [_attachToForm_](CheckBox-Tree-API#wiki-attachtoform)

Each object in the result array has the following properties:
<table>
	<tr>
		<th>Property</th>
		<th>Type</th>
		<th>Optional</th>
		<th>Description</th>
	</tr>
	<tr>
		<td>id</td>
		<td>String | Number</td>
		<td><span class="octicon octicon-x"></span></td>
		<td>Store object id</td>
	</tr>
	<tr>
		<td>value</td>
		<td>String</td>
		<td><span class="octicon octicon-x"></span></td>
		<td>Tree node label. The actual value is determined using the
		<a href="Model-API#wiki-labelAttr">labelAttr</a> property of the model</td>
	</tr>
	<tr>
		<td>checked</td>
		<td>Boolean | String</td>
		<td><span class="octicon octicon-x"></span></td>
		<td>The current checked state of the store object ("mixed", `true` or `false`)</td>
	</tr>
	<tr>
		<td>count</td>
		<td>Number</td>
		<td><span class="octicon octicon-x"></span></td>
		<td>
			The number of times the store object is found in the Checkbox tree. If zero,
			there currently is **_NO_** tree node for the given store object.
			If count is greater than one, it indicates there are multiple tree nodes for
			the given store object. (e.g. this is a [multi parented](Store-API#wiki-multiparented) tree).
		</td>
	</tr>
	<tr>
		<td>virtual</td>
		<td>Boolean</td>
		<td><span class="octicon octicon-check"></span></td>
		<td>
			If present and `true`, it indicates the object is associated with the fabricated
			root of the tree and does **_NOT_** represents an object in the store. This
			property is only present if the Checkbox Tree is used in combination with a
			_ForestStoreModel_ and the _domOnly_ parameter is `false` (default).
		</td>
	</tr>
</table>

Example result array:
```javascript
[
  {id: "$root$", value: "Family", checked: "mixed", count: 1, virtual: true},
                          ...
  {id: "bart", value: "bart", checked: true, count: 1},
  {id: "lisa", value: "lisa", checked: false, count: 1},
  {id: "todd", value: "todd", checked: false, count: 0},
                          ...
]
```

In case of a multi parented tree, multiple checkboxes may refer to the same store object
however, the result array will only have one entry for the given store object.



<h2 id="loading-the-extension">Loading the Extension</h2>

The TreeOnSubmit extension can be loaded programmatically or automatically. To load the
TreeOnSubmit extension programmatically, simply include `cbtree/extension/TreeOnSubmit` in
your require statement like:

```javascript
require(["cbtree/Tree",
         "cbtree/extension/TreeOnSubmit"
        ], function (CBTree) {
              ...
});
```

The Checkbox Tree can also load the _TreeOnSubmit_ extension automatically by simply declaring
the tree property [_attachToForm_](CheckBox-Tree-API#wiki-attachtoform) as an object:

```javascript
require(["cbtree/Tree"], function (CBTree) {
    var myTree = new CBTree({attachToForm: {name: "checkboxes", checked: true}, ... );
              ...
});
```

Alternatively:

```javascript
require(["cbtree/Tree"], function (CBTree) {
    var myTree = new CBTree({ ... });
    myTree.set("attachToForm", {name: "checkboxes", checked: true});
              ...
});
```

Whenever the Checkbox Tree detects the tree property [_attachToForm_](CheckBox-Tree-API#wiki-attachtoform)
to be an object, it will automatically try to load the _TreeOnSubmit_ extension if it isn't
loaded already.



<h2 id="use-custom-extension">Use Custom Extension</h2>

As stated above, the _TreeOnSubmit_ extension effectively replaces the Checkbox Tree's
`onSubmit()` method. There are three different ways you can establish your own **submit**
event listener or onSubmit method.

1.	Establish an event listener calling the `tree.on()` method.
2.	Passing your `onSubmit()` method as a CheckBox Tree parameter.
3.	Write your own extension containing an `onSubmit()` method.

### The Checkbox Tree onSubmit Method

The Checkbox Tree `onSubmit()` method is called if, and only if, the submit button on the
form is clicked **_AND_** the form includes an instance of the Checkbox Tree.
The Checkbox Tree `onSubmit()` method has the following signature:

<table>
	<tr>
		<td  colspan="3"><b>onSubmit</b>( <em>formNode, treeWidget, event</em> )</td>
	<tr>
	<tr>
		<th>parameter</th>
		<th>type</th>
		<th>description</th>
	</td>
	<tr>
		<td>formNode</td>
		<td>DOM node</td>
		<td>DOM node associated with the HTML &lt;form> element</td>
	</tr>
	<tr>
		<td>treeWidget</td>
		<td>CBTree</td>
		<td>Instance of the Checkbox Tree included in the form</td>
	</tr>
	<tr>
		<td>event</td>
		<td>DOM Event</td>
		<td>Native DOM event of type <b>submit</b></td>
	</tr>
	<tr>
		<td><b>returns:</b></td>
		<td>Boolean</td>
		<td>
			Boolean or <code>undefined</code>. If false, the default action for the
			<b>submit</b> event is canceled.
		</td>
	</tr>
</table>

<span class="mega-octicon octicon-alert"></span> Regardless of, if you are replacing the
`onSubmit()` method or establishing an **submit** event listener, **DO NOT** change the checked state
of any checkbox node. If you need to change the checked state, get the tree node widget associated
with the checkbox and use the widget accessor `set()` instead.

```javascript
var treeNodeWidget = dijit.getEnclosingWidget(checkboxNode).getParent();
treeNodeWidget.set("checked", true);
```


### Establish Event Listener

```javascript
tree.on("submit", function (formNode, treeWidget, evt) {
    if (!evt.defaultPrevented) {
        // Your code goes here...
    }
    return !evt.defaultPrevented;
});
```

As an example, if you want to fetch all checkbox nodes and their parent tree node widgets
in your **submit** event listener, consider the following example:

```javascript
require(["cbtree/Tree", ... ], function (CBTree, ...) {
    var tree = new CBTree({attachToForm: true, ...});
                      ...
    tree.on("submit", function (formNode, treeWidget, evt) {
        if (!evt.defaultPrevented) {
            var checkboxNodes   = dojo.query(".dijitCheckBoxInput", treeWidget.domNode);
            var treeNodeWidgets = checkboxNodes.map(function (domNode) {
                return dijit.getEnclosingWidget(domNode).getParent();
            });
                      ...
        }
        return !evt.defaultPrevented;
    });
});
```


### Pass onSubmit method as an argument

```javascript
require(["cbtree/Tree", ... ], function (CBTree, ... ) {
    var myTree = new CBTree({
        onSubmit: function (formNode, treeWidget, evt) {
            if (!evt.defaultPrevented) {
                // Your code goes here...
            }
            return !evt.defaultPrevented;
        },
        ... }, "CheckBoxTree" );
});
```

### Write your own Extension

```javascript
define(["cbtree/Tree", ... ], function (CBTree, ... ) {
    "use strict";

    return CBTree.extend({
        _hasOnSubmit: true,    // Required

        onSubmit: function (formNode, treeWidget, evt) {
            if (!evt.defaultPrevented) {
                // Your code goes here...
            }
            return !evt.defaultPrevented;
        }
    });
});
```

To have your extension loaded automatically, set the [attachToForm](CheckBox-Tree-API#wiki-attachtoform)
property _extension_ to the path of your extension.

```javascript
var myTree = new CBTree({attachToForm: {extension: "cbtree/extension/myOnSubmit"}}, ... );
```


<h2 id="sample-application">Sample Application</h2>

The following is a simple application displaying a tree inside a HTML form. When the
submit button is clicked, the tree's `onSubmit()` event listener is magically called to
retrieve the checked state of all store objects and attach them to the form. On successful
completion the form is submitted using the HTTP POST method.

Notice that the application is not required to set the _onsubmit_ attribute of the form or
even load the _TreeOnSubmit_ extension. Simply defining the tree property _attachToForm_
as an object triggers all the required magic.

```html
<!DOCTYPE html>
 <html>
    <head>
        <style type="text/css">
            @import "../../../dijit/themes/claro/claro.css";
            @import "../../themes/claro/claro.css";
        </style>

        <script type="text/javascript">
            var dojoConfig = {
                async: true,
                baseUrl: "../../../",
                packages: [
                    { name: "dojo",    location: "dojo" },
                    { name: "dijit", location: "dijit" },
                    { name: "cbtree",location: "cbtree" }
                ]
            };
        </script>
        <script type="text/javascript" src="../../../dojo/dojo.js"></script>
        <script type="text/javascript">
            require([
                "dojo/ready",
                "cbtree/Tree",
                "cbtree/model/TreeStoreModel",
                "cbtree/store/Hierarchy"
            ], function(ready, Tree, Model, Store) {

                var data = [
                    { id: "earth", name:"The earth", type:"planet"},
                    { id: "AF", name:"Africa", type:"continent", parent: "earth"},
                        { id: "EG", name:"Egypt", type:"country", parent: "AF" },
                        { id: "KE", name:"Kenya", type:"country", parent: "AF" },
                            { id: "Nairobi", name:"Nairobi", type:"city", parent: "KE" },
                            { id: "Mombasa", name:"Mombasa", type:"city", parent: "KE" },
                        { id: "SD", name:"Sudan", type:"country", parent: "AF" },
                            { id: "Khartoum", name:"Khartoum", type:"city", parent: "SD" },
                    { id: "AS", name:"Asia", type:"continent", parent: "earth" },
                        { id: "CN", name:"China", type:"country", parent: "AS", bingo: false },
                        { id: "IN", name:"India", type:"country", parent: "AS" },
                        { id: "RU", name:"Russia", type:"country", parent: "AS" },
                        { id: "MN", name:"Mongolia", type:"country", parent: "AS" },
                    { id: "OC", name:"Oceania", type:"continent", population:"21 million", parent: "earth"},
                        { id: "AU", name:"Australia", type:"country", population:"21 million", parent: "OC"},
                    { id: "EU", name:"Europe", type:"continent", parent: "earth" },
                        { id: "DE", name:"Germany", type:"country", parent: "EU" },
                        { id: "FR", name:"France", type:"country", parent: "EU" },
                        { id: "ES", name:"Spain", type:"country", parent: "EU" },
                        { id: "IT", name:"Italy", type:"country", parent: "EU" }
                ];
                var store = new Store({data: data});
                var model = new Model({
                    store: store,
                    query: {id: "earth"},
                    rootLabel: "Earth",
                    checkedRoot: true}
                );

                ready(function() {
                    var tree = new Tree({
                        model: model,
                        attachToForm: {
                            checked:["mixed", true],
                            name: "checkboxes"
                        }
                    }, "CheckboxTree" );
                    tree.startup();
                });
            });
        </script>
    </head>

    <body class="claro">
      <form name="someForm" action="cbtree.php" method="post">
        <div id="CheckboxTree">
        </div>
        <input type="submit" value="Submit">
      </form>
    </body>
</html>```

The form, in the above example, uses the HTTP POST method which forces the _form data set_ to
be included in the HTTP request body as apposed to the _Query String_ of a URL when using
the HTTP GET method. Using the HTTP POST method has two benefits:

1. There is no length constraint on the _form data set_ as there would be with HTTP GET.
2. The URL cannot be bookmarked.

Bookmarking a page based on the checked state of store objects doesn't really make sense.

### The Server Application

The server side application can be as simple as:

```html
<?php
    $storeItems = json_decode($_POST["checkboxes"]);
    forEach($storeItems as $item) {
        $objectId = $item->id;
        $checked  = $item->checked;
        $label    = $item->value;
        $count    = $item->count
        // do something .....
               ...
    }
?>
```

In the above example the $_POST_ property **_checkboxes_** is fetched because that's name set
for the _attachToForm_ tree property. The default property name would be **_checkedStates_**.
