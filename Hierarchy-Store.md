The cbtree Hierarchy Store is devired from the cbtree [Memory and Natural](Store#store-types)
Store. The Hierarchy Store is capable of, as the name implies, maintaining the
hierarchy and natural order amongst store objects. To do this the store adds a
so-called parent property to each object in the store and optionally maintains
an index of all children of store objects.

The hierarchy store and the derived Object Store are the preferred store types
in a CheckBox Tree environment.

<h3>Content <span class="mega-octicon octicon-book"></span></h3>
* [Store Properties](#store-properties)
* [Data Hierarchy](#data-hierarchy)
* [Mutli Parented Objects](#multi-parented-objects)



<h2 id="store-properties">Store Properties</h2>
To enable the ability to maintain an object hierarchy this type of store implements
three additional properties:

<h3 id="">indexChildren:</h3>
> **_TYPE_**: Boolean

> If true the store will maintain a separate index for the children of store
> objects. If the index is available it will be used by the store method
> [getChildren()](Store-API#wiki-getchildren) improving performance.
> This property is an extension to the **cbtree/store/api/Store** API properties.

> **_DEFAULT_**: true

<h3 id="">multiParented:</h3>
> **_TYPE_**: Boolean | String

> Indicates if the store will support multi-parented objects. Multi Parented
> objects are objects that can have more than one parent. If set to "auto"
> the store will inspect the data loaded into the store to determine if this
> property is to be set to `true` or `false`.

> **_DEFAULT_**: "auto"

<h3 id="parentproperty">parentProperty:</h3>
> **_TYPE_**: String

> The property name of an object whose value represents the object's parent id
> or ids.

> **_DEFAULT_**: "parent"




<h2 id="data-hierarchy">Data Hierarchy</h2>

In essence every `dojo/store` or derivative, including the `cbtree/store`, store
is, in most cases, a flat array of JavaScript objects without a natural order or
hierarchy. Lets take a look at a simple set of object as an example:

```javascript
[
  { name:"Audi" ,type:"factory" },
  { name:"BMW"  ,type:"factory" },
  { name:"A3"   ,type:"sedan" },
  { name:"Q5"   ,type:"suv" },
  { name:"M3"   ,type:"sedan" },
  { name:"535"  ,type:"sedan" },
  { name:"750"  ,type:"sedan" }
]
```
The above objects all have a name and some type but there is no way we could
derive some sort of hierarchical relationship. To create a hierarchy we need
an additional object property, like *parent*, that defines the relation between
the objects.  
Both the cbtree **Hierarchy** and **Object** store provide support for a parent
property which enable them to fetch either the children or the parent(s) of an
object. The actual object property to be used is set using the store's 
[parentProperty](Store-API#wiki-parentproperty) attribute. The default value
for this property is "parent".

```javascript
[
  { name:"Audi" ,type:"factory" },
  { name:"BMW"  ,type:"factory" },
  { name:"A3"   ,parent:"Audi", type:"sedan" },
  { name:"Q7"   ,parent:"Audi", type:"suv" },
  { name:"M3"   ,parent:"BMW" , type:"sedan" },
  { name:"535"  ,parent:"BMW" , type:"sedan" },
  { name:"750"  ,parent:"BMW" , type:"sedan" }
]
```
Above we have added a **_parent_** property to those objects who have an obvious
parent in our store. However, the objects 'Audi' and 'BMW' still don't have a 
*parent* property. Please note that instead of the default "parent" property
name we could have used a different property name like "manufaturer" and create
the store as follows:

```javascript
var myStore = new Hierarchy( {data:carData, idProperty:"name", parentProperty:"manufaturer", ...});
```
The updated set of objects now has a limited hierarchy, that is, not all objects
have a parent. In order to create a hierachy with a single root object, add one
more object that serves as the parent of 'Audi' and 'BMW'.

```javascript
[
  { name:"Cars" ,parent: null , type:"toys" },
  { name:"Audi" ,parent:"Cars", type:"factory" },
  { name:"BMW"  ,parent:"Cars", type:"factory" },
  { name:"A3"   ,parent:"Audi", type:"sedan" },
  { name:"Q7"   ,parent:"Audi", type:"suv" },
  { name:"M3"   ,parent:"BMW" , type:"sedan" },
  { name:"535"  ,parent:"BMW" , type:"sedan" },
  { name:"750"  ,parent:"BMW" , type:"sedan" },
]
```
Because we now have an object store with a single root object we could select
a Tree Model with this store instead of a Forest Model. See the 
[Tree Store Model](Model#wiki-tree-store-model) for more details.

```javascript
require(["cbtree/Tree", 
         "cbtree/store/Hierarchy", 
         "cbtree/model/TreeStoreModel"
        ], function (Tree, Hierarchy, TreeStoreModel) {

  var store = new Hierarchy( {data:carData, idProperty:"name", multiParented:false} );
  var model = new TreeStoreModel( {store:store, query:{type:"toys"}} );
  var tree  = new Tree( {model:model}, "cars-div" );
  tree.startup();
});
```
Notice that we explicitly set the store property [multiParented](#multiParented)
to `false` because we already known these are single parent objects and thus
there is no need for the store to inspecting the objects to determine the 
property value.

To fully exploit the store hierarchy see the [Ancestry](Ancestry-Extension) extension.




<h2 id="multi-parented-objects">Multi Parented Objects</h2>
Multi parented objects are objects that have more than one parent. A perfect
example is a family tree were each child has two parents. A parent, in the 
context of a store, is an abstract data type that merely defines some sort of
relation between objects. 
If, for example, your store represents geographical data including a train station,
the station object could have a parent called "Transportation Hub" but also a
parent called "Point Of Interest".

If the store property [multiParented](#multiparented) is set to true, the
value of the object's parent property is stored as an array otherwise the value
is stored as a single value. 

By default the store property _multiParented_ is set to "auto" meaning that the
data loaded into the store is inspected to determine the correct setting of 
the property. If any object loaded into the store, during the initial store
load, has a parent property whose value is an array the _multiParented_ property
is automatically set to `true` otherwise it is set to `false`.

The hierarchy store puts no limitation on the number of parents an object can
have.

### Defining Multi Parented Objects
To define a multi parented object the object's parentProperty value must be 
expressed as an array of parent object ids. The following illustrates how to
define multi parented objects, the stores *idProperty* is set to "name".

```javascript
[
  { name:"Homer", type:"parent" },
  { name:"Marge", type:"parent" },
  { name:"Bart",  type:"child", parent:["Homer","Marge"] }
]
```
In the following example the parentProperty value *childOf* is used instead of
the default value *parent*.

```javascript
require(["dojo/ready", 
         "cbtree/Tree", 
         "cbtree/store/Hierarchy", 
         "cbtree/model/ForestStoreModel"
        ], function(ready, Tree, Hierarchy, ForestStoreModel) {

    var geoData = [
        { id:"Transportation Hub", type:"hub" },
        { id:"Points Of Interest", type:"POI" },
        { id:"Central Station", type: "station", childOf:["Transportation Hub","Points Of Interest"] }
      ];

    var store = new Hierarchy( { data: geoData, parentProperty:"childOf" });
    var model = new ForestStoreModel( { store: store,
                                        query: {type:["hub","POI"]},
                                        rootLabel: "Geo Data",
                                        labelAttr:"id",
                                        checkedRoot: true
                                      });
    ready(function(){
      var tree = new Tree( { model: model }, "CheckboxTree" );
      tree.startup();
    });
  });
```



<span class="mega-octicon octicon-alert"></span>
Be aware that if the store property _multiParented_ is set to `false` and the data
loaded into the store is actually multi-parented only the first parent for each
object is stored. In addition, calling the store method `addParent()` will
replace the existing parent instead of adding one.
