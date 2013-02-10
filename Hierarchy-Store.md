The cbtree Hierarchy Store is devired from the cbtree [Memory and Natural](Store#store-types)
Store. The Hierarchy Store is capable of, as the name implies, maintaining the
hierarchy and natural order amongst store objects. To do this the store adds a
so-called parent property to each object in the store and optionally maintains
an index of all children of store objects.

The hierarchy store and the derived Object Store are the preferred store types
in a CheckBox Tree environment.

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

<h2 id="multi-parented-objects">Multi Parented Objects</h2>
Multi parented objects are objects that have more than one parent. A perfect
example is a family tree were each child has two parent. A parent, in the 
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

<span class="mega-icon mega-icon-exclamation"></span>
Be aware that if the store property _multiParented_ is set to false and the data
loaded into the store is actually multi-parented only the first parent for each
object is stored. In addition, calling the store method `addParent()` will
replace the existing parent instead of adding one.

