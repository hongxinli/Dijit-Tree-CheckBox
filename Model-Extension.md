
The Store Model Extension, as the name implies, extends the functionality of 
the CheckBox Tree Store Models. The Store Model Extension can be loaded
and used with the TreeStoreModel, the ForestStoreModel and the FileStoreModel. 

The model extension allows the user to programmatically manipulate checkbox 
trees. You can simply check/uncheck a single store item or a set of store items
using a store query. 

```javascript
require(["cbtree/model/TreeStoreModel",
         "cbtree/model/StoreModel-EXT",    // Load model extension
                   ... 
        ], function (TreeStoreModel, StoreModel-EXT, ... ) {

});
```


******************************************

<h3 id="">addParent( storeItem, parentId )</h3>
> Add a new parent to a store item.

**_storeItem:_** Object

**_parentId:_** String

******************************************

<h3 id="check">check( query, onComplete?, scope?, storeOnly? )</h3>
> Check all store items that match the query.

**_query:_** Object | String
> A query object or string.	If query is a string the [idProperty](Store-API#wiki-idproperty)
> attribute of the store is used as the query attribute and the query string
> assigned as the associated value.
> (See [The Query Argument](Query-Engine#wiki-the-query-argument) for more details).

**_onComplete:_** Function
> If an onComplete callback is specified, the callback function will be called
> just once, after the last storeItem has been updated as: `onComplete(matches, updates)`
> were *matches* equates to the total number of store items that matched the
> query and *updates* equates to the number of store items that required an
> update.

**_scope:_** Object
> If a scope object is provided, the function onComplete will be invoked in the
> context of the scope object. In the body of the callback function, the value
> of the "this" keyword will be the scope object. If no scope is provided, 
> onComplete will be called in the context of the model.

**_storeOnly:_** Boolean
> If the store model property *checkedStrict* is enabled this parameter will be automatically 
> set to *true*.  
> See [fetchItemsWithChecked()](#fetchitemswithchecked) for details.

******************************************

<h3 id="fetchitem">fetchItem( query, onComplete, scope? )</h3>
> Get the store item that matches *query*.

**_query:_** Object | String
> An object or string used to query the store. If query is a string its value is
> assigned to the store idProperty in the query.
> (See [The Query Argument](Query-Engine#wiki-the-query-argument) for more details).

**_onComplete:_** Function
> User specified callback method which is called on completion with the first
> store item that matched the query argument. Method onComplete() is called 
> as: `onComplete(storeItem)` in the context of scope if scope is specified
> otherwise in the active context (this).

**_scope:_** Object
> If a scope object is provided, the function onComplete will be invoked in the
> context of the scope object. In the body of the callback function, the value
> of the "this" keyword will be the scope object. If no scope object is provided,
> onComplete will be called in the context of tree.model.

******************************************

<h3 id="fetchitemswithchecked">fetchItemsWithChecked( query, onComplete, scope?, storeOnly? )</h3>
> Get the list of store items that match the query and have a checked state,
> that is, a property identified by the models [checkedAttr](Model-API#wiki-checkedattr)
property. 

**_query:_** Object | String
> An object or string used to query the store. If query is a string its value is
> assigned to the store idProperty in the query.
> (See [The Query Argument](Query-Engine#wiki-the-query-argument)
for more details).

**_onComplete:_** Function
> User specified callback method which is called on completion with an array of
> store items that matched the query argument. Method onComplete is called
> as: `onComplete(storeItems)` in the context of scope if scope is specified
> otherwise in the active context (this).

**_scope:_** Object
> If a scope object is provided, the function onComplete will be invoked in the
> context of the scope object. In the body of the callback function, the value
> of the "this" keyword will be the scope object. If no scope is provided, 
> onComplete will be called in the context of the model.

**_storeOnly:_** Boolean
> Indicates if the fetch operation should be limited to the in-memory store
> only. Some stores may fetch data from a back-end server when performing a
> deep search. When querying store item properties, some properties may ***ONLY***
> be available in the in-memory store as is the case with a File Store.
> As an example, the *checked* state of a store item is an attribute in the 
> in-memory File Store, custom created by the store model but not available on,
> or maintained by, the back-end server.

> Limiting the fetch operation to the store will prevent it from requesting, 
> potentially large, datasets from the server that don't have the required 
> properties to begin with. However, limiting a fetch to the in-memory store
> may not return all possible matches if the store isn't fully loaded. 
> For example, if lazy loading is used and not all tree branches have been fully
> expanded the result of a fetch may be incomplete.

> The default value for *storeOnly* is *true*.

******************************************

<h3 id="removeparent">removeParent( storeItem, parentId )</h3>
> Remove a parent from a store item.

**_storeItem:_** Object

**_parentId:_** String


******************************************

<h3 id="uncheck">uncheck( query, onComplete?, scope?, storeOnly? )</h3>
> Uncheck all store items that match the query.

**_query:_** Object | String
> A query object or string.	If query is a string the [idProperty](Store-API#wiki-idproperty)
> attribute of the store is used as the query attribute and the query string
> assigned as the associated value.
> (See [The Query Argument](Query-Engine#wiki-the-query-argument) for more details).

**_onComplete:_** Function
> If an onComplete callback is specified, the callback function will be called
> just once, after the last storeItem has been updated as: `onComplete(matches, updates)`
> were *matches* equates to the total number of store items that matched the
> query and *updates* equates to the number of store items that required an
> update.

**_scope:_** Object
> If a scope object is provided, the function onComplete will be invoked in the
> context of the scope object. In the body of the callback function, the value
> of the "this" keyword will be the scope object. If no scope is provided, 
> onComplete will be called in the context of the model.

**_storeOnly:_** Boolean
> If the store model property *checkedStrict* is enabled this parameter will be automatically 
> set to *true*.  
> See [fetchItemsWithChecked()](#fetchitemswithchecked) for details.

