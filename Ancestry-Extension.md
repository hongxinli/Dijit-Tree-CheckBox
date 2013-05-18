The store Ancestry extension adds an additional set of functions to both the
[Hierarchy](Hierarchy-Store) and Object stores to explore the hierarchy and
order of those stores.

<h3>Content <span class="mega-octicon octicon-book"></span></h3>
* [Store Root versus Tree root](##store-root-versus-tree-root)
* [Loading the Ancestry Extension](#loading-the-ancestry-extension)
* [Ancestry Functions](#ancestry-functions)
* [Sample Application](#sample-application)

<h2 id="store-root-versus-tree-root">Store Root versus Tree Root</h2>

Throughout the documentation references are made to both the tree root as well
as the store root. It is important to know that they are **NOT** the same thing.
Store root item(s) identify so called top-level objects in the store, there
can be as little as zero top-level objects (empty store) or as many as the number
of store objects. A top-level store object is an object that does not have a parent
object.

### Store Root
In reality, stores implementing the `cbtree/store/api/Store` or `dojo/store/api/Store`
API do not have distinct root objects. In essence those stores are containers of 
'items' were each item is a JavaScript Object. This also applies to the cbtree
hierarchy and Object stores however, they are able to derive the object
hierarchy using the store property [parentProperty](Store-API#wiki-parentProperty)
and classify the objects as either:

* Top-level store objects.
* Non top-level store objects.

As stated before, top-level store objects are objects that do not have a parent
reference, for example:

```javascript
{ name:"Abe", lastName:"Simpson" }
{ name:"Abe", lastName:"Simpson", parent:null }
{ name:"Abe", lastName:"Simpson", parent:[] }
```
Non top-level store objects are objects that **_do_** have a parent reference
and therefore are descendants (or children) of its parent(s):

```javascript
{ name:"Homer", lastName:"Simpson", parent:["Abe"] }
{ name:"Bart", lastName:"Simpson", parent:["Homer", "Marge"] }
```
In any case, the following statement must always be true:
> *If object B is a descendant of object A, than object A is an ancestor of
> object B*.

Please see the [Data Hierachy](Hierarchy-Store#wiki-data-hierarchy) section for
more details on how to organize your data.

### Tree Root
The tree root is a DOM element that may or may not be associated with a store
object. The tree root is established by executing the root query of the store
model and, depending on the type of store model, may represent an actual store object or a
fabricated root object. See the [Tree Store Model](Model#wiki-tree-store-model)
and [Forest Store Model](Model#wiki-forest-store-model) for more details.

However, in case the tree root **_is_** associated with an actual store object
it still does not have to be a top-level store object. Again, it all depends on
the root query.




<h2 id="loading-the-ancestry-extension">Loading the Ancestry Extension</h2>

Ancestry is implemented as an extension to the cbtree Natural, Hierarchy and
Object stores and as such must to be loaded as a separate module. The following
sample shows how to load the Ancestry extension. The Ancestry extension is
located at **cbtree/store/extensions/Ancestry**

```javascript
require(["cbtree/store/Hierarchy", 
         "cbtree/store/extensions/Ancestry"]  // Load Ancestry extension
  function( Hierarchy, Ancestry ) {
                ...
    var myStore = new Hierarchy( { ... } );
  }
);
```
If you instantiate your store declarative you **MUST** load the Ancestry extension
programmatically, this because it has no constructor and therefore can not be 
instantiated by `dojo/parser`. Note that, although in this case not strictly
required, we also list the hierarchy store as a dependency.

```html
require(["dojo/parser",                      // dojo parser
         "cbtree/store/Hierarchy",           // The Hierarchy store
         "cbtree/store/extensions/Ancestry"  // The Ancestry extensions
        ]);
                ...
<body class="claro">
  <div data-dojo-id="myStore" data-dojo-type="cbtree/store/Hierarchy" data-dojo-props=" ... "></div>
                ...
</body>
```



<h2 id="ancestry-functions">Ancestry Functions</h2>
The following section provides a detailed description of the Ancestry functions:


<h3 id="analyze">analyze()</h3>
> Analyze the hierarchy of the store and reports if there are any broken links.
> All parent references are checked for their presence in the store.

**_maxCount:_** Number
> The maximum number of missing object deteced before the store analysis is
> aborted. If 0 or less all missing objects will be reported.

**returns:** Object || null
> A key:value pairs JavaScript object. Each key represents the identifier of a 
> missing object. The associated value is an array of identifiers referencing
> the missing object. If no object is missing null is returned.

**********************************************

<h3 id="getancestors">getAncestors( item, idOnly? )</h3>
> Get all ancestors (parent, grandparent, great-grandparent, and so forth) of a
> given item.

**_item:_** Object | Id
> Store object or id to evaluate

**_idOnly:_** Boolean
> Optional, If set to true only the ancestor ids are returned. The default is 
> `false`.

**returns:** Object[] | void
> If the item exists, an array of store object or ids otherwise undefined.
> If an empty array is returned (length=0) it indicates that item has no
> parents and therefore is considered a top-level item.

**********************************************

<h3 id="getdescendants">getDescendants( item, idOnly? )</h3>
> Get all descendants (children, grandchildren, great-grandchildren, and so forth)
> in hierarchical order of a given item.

**_item:_** Object | Id
> Store object or id to evaluate

**_idOnly:_** Boolean
> Optional, If set to true only the descendant ids are returned. The default is 
> `false`.

**returns:** Object[] | void
> If the item exists, an array of store object or ids otherwise undefined.
> If an empty array is returned (length=0) it indicates that item has no
> parents and therefore is considered a top-level item.

**********************************************

<h3 id="getpaths">getPaths( item, separator? )</h3>
> Returns the virtual path(s) of an item. Each path segment represents the 
> identifier of an ancestor with the exception of the last segment	which is
> the item identifier. The default format of a path is a URI.

**_item:_** Object | Id
> The item whose path(s) are to be returned. Item is either an object	or an
> identifier.

**_separator:_** String
> Optional, specifies the character to use for separating the path segments.
> Default is the forward slash (/).

**returns:** PathList
> If the item exists a PathList otherwise undefined. A PathList is an array 'like'
> object.

**********************************************

<h3 id="getsiblings">getSiblings( item, idOnly? )</h3>
> Get the siblings of an item.

**_item:_** Object | Id
> Store object or id to evaluate

**_idOnly:_** Boolean
> Optional, If set to true only the sibling ids are returned. The default is 
> `false`.

**returns:** Object[] | void
> If the item exists, an array of store object or ids otherwise	undefined. 
> If an empty array is returned (length=0) it indicates that item has no
> siblings.

**********************************************

<h3 id="isancestorof">isAncestorOf( ancestor, item )</h3>
> Returns true if the object identified by argument `ancestor` is an ancestor
> of the object identified by argument `item`.

**_ancestor:_** Object | Id
> The ancestor Object or id

**_item:_** Object | Id
> Child object or id to evaluate

**returns:** Boolean
> true if ancestor is an ancestor of item otherwise false.

**********************************************

<h3 id="isbefore">isBefore( itemA, itemB )</h3>
> Evaluate if objectA appears before objectB in the natural order of store objects.

**_itemA:_** Object | Id
> The first Object or id

**_itemB:_** Object | Id
> The second Object or id

**returns:** Boolean
> true if itemA appears before itemB otherwise false.

**********************************************

<h3 id="ischildof">isChildOf( item, parent )</h3>
> Evaluate if an item is a child (or immediate descendant) of a given parent.

**_item:_** Object | Id
> Child object or id to evaluate

**_parent:_** Object | Id
> The parent Object or id

**returns:** Boolean
> true if the object is a child otherwise false.

**********************************************

<h3 id="isdescendantof">isDescendantOf( item, ancestor )</h3>
> Evaluate if an item is a descendant of a given parent.

**_item:_** Object | Id
> Child object or id to evaluate

**_ancestor:_** Object | Id
> The ancestor Object or id

**returns:** Boolean
> true if the child object is a descendant otherwise false.

**********************************************

<h3 id="issiblingof">isSiblingOf( item, sibling )</h3>
> Returns true if the object identified by argument 'item' is the sibling of
> the object identified by argument 'sibling'.

**_item:_** Object | Id
> Store object or id to evaluate

**_sibling:_** Object | Id
> The sibling Object or id

**returns:** Boolean
> true if item is a sibling otherwise false.



<h2 id="sample-application">Sample Application</h2>
The following is demo application `cbtree/demos/store/tree30.html`.

```html
<!DOCTYPE html>
<html>
  <head>
    <meta charset="utf-8">
    <title>The Store Hierarchy and Ancestry</title>
    <style type="text/css">
      @import "../../../dijit/themes/claro/claro.css";
      @import "../../themes/claro/claro.css";
    </style>

    <script type="text/javascript">
      var dojoConfig = {
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
        "cbtree/store/Hierarchy",          // The Hierarchy store
        "cbtree/store/extensions/Ancestry" // Load the Ancestry extension
        ], function ( ready, Tree, TreeStoreModel, Hierarchy, Ancestry) {

          var store = new Hierarchy( { url: "../../store/json/Simpsons.json", idProperty:"name"});
          var model = new TreeStoreModel( { store: store,
                                            query: {name: "Root"},
                                            rootLabel: "The Family",
                                          });

          function checkBoxClicked( item, nodeWidget, event ) {
            var descendants = store.getDescendants(item);
            var ancestors   = store.getAncestors(item, true);
            var siblings    = store.getSiblings(item);

            var isDecendant = store.isDescendantOf(item,"Abe");
            var isAncestor  = store.isAncestorOf("Abe", item);

            var paths = store.getPaths( item );
            if (paths.length) {
              var intersect = paths.intersect();
              var contains  = paths.contains( /Abe/);
              var segments  = paths.segments();
            }
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
    <h1 class="DemoTitle">Store Hierarchy and Ancestry</h1>
    <div id="CheckboxTree">
    </div>
  </body>
</html>
```
