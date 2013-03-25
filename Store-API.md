
The **cbtree/store/api/Store** API is an extension to the **dojo/store/api/Store** API. Therefore, any store
that exposes the **dojo/store/api/Store** API is by default compliant with this API definition.

#### <span class="mega-icon mega-icon-exclamation"></span> IMPORTANT

This API defines function signatures **ONLY** without providing implementation details.
All store functions and properties defined in this API are optional and require implemention
**ONLY** if the store is to offer the functionality. Therefore, your store of choice may
only support a subset of this API.

Except for hasChildren() and isItem(), every function, whose return value isn't already
a promise or void, **MUST** return a promise as its return value if the execution of the
function is asynchronous.
Whenever a store function returns a promise, the following conditions **MUST** be met:

1. On successful completion of the function the promise must resolve
with the specified synchronous return value as its result.
2. On exception, the promise is rejected with the error condition (exception) as its result.

Please refer to the store specific documentation and 
[Store API Matrix](Store#wiki-store-api-matrix) to see which properties and
functions are supported.


In the context of this API, property values, arguments and operand types are defined as follows:

<table>
	<tbody>
		<thead>
			<tr>
				<th>Operand</th>
				<th>Definition</th>
			</tr>
		</thead>
		<tr>
			<td><em>Boolean</em></td>
			<td>JavaScript operand of type Boolean</td>
		</tr>
		<tr>
			<td><em>id</em></td>
			<td>String or Number</td>
		</tr>
		<tr>
			<td><em>Number</em></td>
			<td>JavaScript operand of type Number</td>
		</tr>
		<tr>
			<td><em>Object</em></td>
			<td>
				JavaScript key:value pairs object (hash).
			</td>
		</tr>
		<tr>
			<td><em>String</em></td>
			<td>JavaScript operand of type String</td>
		</tr>
		<tr>
			<td><em>void</em></td>
			<td>JavaScript operand of type Undefined</td>
		</tr>
	</tbody>
</table>

<h3>Content <span class="mega-icon mega-icon-readme"></span></h3>
* [Store Properties](#store-properties)
* [Store Functions](#store-functions)
* [Store Directives](#store-directives)





<h2 id="store-properties">Store Properties</h2>
Store properties enable or disable specific features or define characteristics
of a store. The property names also represent properties in the keyword object
passed to the store constructor. For example:

```javascript
require(["cbtree/store/Memory"], function (Memory) {
  var keywordArgs = {url:"some/path/myData", multiParented:true, defaultProperties:{checked:true}};
  var store = new Memory( keywordArgs );
}
```

<h3 id="autoload">autoLoad:</h3>
> **_TYPE_**: Boolean

> Indicates if the store data should be loaded immediately when available or
> deferred until the store method `load()` is called.
> See the store [load](#load) function for additional information.

> **_DEFAULT_**: true


<h3 id="clearonclose">clearOnClose:</h3>
> **_TYPE_**: Boolean

> Indicates if the store content is to be deleted when the store is closed. The
> store is closed by calling the store `close()` function.

> **_DEFAULT_**: false


<h3 id="data">data:</h3>
> **_TYPE_**: Object [] | Any

> An array of JavaScript key:value pairs objects to be loaded into the store as
> the initial store content. If data is any other data type, the store must also
> implement [dataHandler](#datahandler) and [handleAs](#handleas).
> See also [url](#url)

> **_DEFAULT_**: null

<h3 id="datahandler">dataHandler:</h3>
> **_TYPE_**: Function | Object

> The data handler for the store data or server response. A data handler convert
> an arbitrary data format into an array of JavaScript key:value pairs objects
> ready for consumption by the store. If _dataHandler_ is an key:value pairs
> object, the object should support the following properties:
>
>		{ handler: Function|Object,
>		  options: Object?
>		}
>
> If the handler property is an object the object MUST have a property
> named 'handler' whose value is a function.	In this case the handler
> object provides	the scope/closure for the handler function	and the
> options, if any, are mixed into the scope. For example:
>
>     dataHandler: { handler: csvHandler,
>                    options: { fieldNames:["col1", "col2"] }
>                  }
>
> The handler function has the following signature:
>
>		handler( response )

> The response argument is a JavaScript key:value pairs object with a
> "text" and/or "data" property.

> **_DEFAULT_**: null

<h3 id="defaultproperties">defaultProperties:</h3>
> **_TYPE_**: Object

> A JavaScript key:values pairs object whose properties and their value are
> added to new store objects if such properties are missing from a new store
> object.

> **_DEFAULT_**: null

<h3 id="filter">filter:</h3>
> **_TYPE_**: Object | Function

> Query style filter object or function applied to the store data prior to
> populating the store. The filter property can be used to load a subset of
> objects into the store. See also [LoadDirectives](#LoadDirectives)

> **_DEFAULT_**: null

<h3 id="handleas">handleAs:</h3>
> **_TYPE_**: String

> Specifies how to interpret the payload returned in a server response
> or the data passed to a store method responsible for populating the
> store, if any. (typically the store constructor or load() method).

> **_DEFAULT_**: null

<h3 id="hierarchical">hierarchical:</h3>
> **_TYPE_**: Boolean (read-only)

> Indicates if the store is capable of maintaining an object hierarchy.
> The cbtree Models tests for the presence of this property in order to
> determine if it has to set the parent property of an object or if the
> store will handle it.
> If true, the store MUST implement all logic required to support the
> [PutDirective](#PutDirectives) property "parent" and optionally the store 
> property [parentProperty](#parentproperty).

> **_DEFAULT_**: false

<h3 id="idproperty">idProperty:</h3>
> **_TYPE_**: String | Number

> If the store has a single primary key, this indicates the property to use as the
> identity property. The values of this property should be unique.

> **_DEFAULT_**: "id"

<h3 id="">multiParented:</h3>
> **_TYPE_**: Boolean | String

> Indicates if the store is to support multi-parented objects. Multi Parented
> objects are objects that can have more than one parent.

> **_DEFAULT_**: false

<h3 id="parentproperty">parentProperty:</h3>
> **_TYPE_**: String

> The property name of an object whose value represents the object's parent id
> or ids.

> **_DEFAULT_**: "parent"

<h3 id="queryengine">queryEngine:</h3>
> **_TYPE_**: Function

> If the store can be queried locally (on the client side in JS), this property
> defines the query engine to use for querying the store data.
> The signature of the queryEngine function is as follows:
>
>		queryEngineFunction( queryObject, queryOptions? )
>
> The queryEngine function MUST return a function that can execute the provided query
> on an array of objects. For example:

>		var query = store.queryEngine( {foo:"bar"}, {count:10} );
>		query(someArray) -> filtered array

> The returned query function may have a "matches" property that can be
> used to determine if an object matches the query. For example:

>		query.matches( {id:"some-object", foo:"bar"} ) -> true
>		query.matches( {id:"some-object", foo:"something else"} ) -> false

> **_DEFAULT_**: null

<h3 id="url">url:</h3>
> **_TYPE_**: String

> The Universal Resource Location (URL) to retrieve the store data from.

> **_DEFAULT_**: null


<h2 id="store-functions">Store Functions</h2>
As stated above, all store functions are optional and may return a dojo/promise/Promise which then resolves
with the synchronous result of the store function.  

**********************************************

<h3 id="add">add( object, options? )</h3>
>	Add a new object to the store, throws an exception if an object with the same identifier already exists.

**_object:_** Object

> The object to be added to the store.

**_options:_** [PutDirectives](#PutDirectives)?
> Additional directives for creating objects.

**returns:** id


**********************************************

<h3 id="addparent">addParent( child, parents )</h3>
> Add parent or parents to the list of parents of child.

**_child:_** Object
> Store object to which the parent(s) are added.

**_parents:_** (Object | Id) []?
> Object or id or an array of objects or ids to be added as the parent(s) of child.

**returns:** Boolean
> true if parents were successfully added otherwise false.

**********************************************

<h3 id="close">close( clear? )</h3>
> Close the store. Unless the parameter *clear* is set to `true` this function
> has no effect.

**_clear:_** Boolean
> Indicates if the store content is to be deleted. If omitted the store property
> *clearOnClose* is used instead.


**********************************************

<h3 id="destroy">destroy()</h3>
> Release all memory and mark store as destroyed.


**********************************************

<h3 id="get">get( id )</h3>
> Retrieves an object by its identity

**_id:_** String | Number

**returns:** Object | void
> The object found otherwise void.

**********************************************

<h3 id="getchildren">getChildren( parent, options? )</h3>
> Retrieves the children of an object.

**_parent:_** Object
> The object to retrieve the children for.

**_options:_** [QueryDirectives](#QueryDirectives)?
> Additional options to apply to the retrieval of the children.

**returns:** dojo/store/api/Store.QueryResults
> Note: An empty _QueryResults_ may indicate the parent does not have children or
> the parent itself was not found in the store.

**********************************************

<h3 id="getidentity">getIdentity( object )</h3>
> Returns an object's identity. See also the store property "idProperty"

**_object:_** Object
> The object to get the identity from.

**returns:** id | void

**********************************************

<h3 id="getparents">getParents( child )</h3>
> Retrieve the parent(s) of a store object.

**_child:_** Object
> The store object to retrieve the parent(s) for.

**returns:** Object[] | void

**********************************************

<h3 id="haschildren">hasChildren( parent )</h3>
> Returns boolean true if a parent object has known children otherwise false.

**_parent:_** Object
> The object to evaluate.

**returns:** Boolean
> true if the parent object has known children otherwise false.

**********************************************

<h3 id="isitem">isItem( object )</h3>
> Evaluate if an object is a valid member of this store, that is, it came from this store
> instance. This method MUST execute synchronously.

**_object:_** Object
> Object to evaluate

**returns:** Boolean
> true if the object is a member of this store otherwise false.

**********************************************

<h3 id="load">load( options? )</h3>
> Load the store data using the data identified by the store property [data](#data)
> or [url](#url).

**_options:_** [LoadDirectives](#LoadDirectives)?
> Optional load directives.

**returns:** dojo/promise/Promise
> On successful completion the promise resolves to void. On error the promise is
> rejected with the error condition as its result.

**********************************************

<h3 id="put">put( object, options? )</h3>
> Stores an object. Throws an exception if an object with the same identification
> already exists **AND** the PutDirective.overwrite is set to false.

**_object:_** Object
> The object to store.

**_options:_** [PutDirectives](#PutDirectives)?
> Additional directives for storing objects.

**returns:** id

**********************************************

<h3 id="query">query( query, options? )</h3>
> Queries the store for objects. The query function does not alter the store, but returns a set
> of objects from the store.

**_query:_** String | Object | Function
> The query to use for retrieving objects from the store.

**_options:_** [QueryDirectives](#QueryDirectives)?
> The optional arguments to apply to the resultset.

**returns:** dojo/store/api/Store.QueryResults
> The results of the query, extended with iterative methods.

**********************************************

<h3 id="ready">ready( callback?, errback?, scope? )</h3>
> Execute the callback when the store data has been loaded. If an error occurred
> during the loading process errback is called instead.

**_callback:_** Function
> Function called on successful load of the store data.

**_errback:_** Function
> Function called if an error occurred during the store load.

**_scope:_** Object
> The scope or context in which the callback and errback functions are executed.
> If omitted both functions will be executed in the scope of the store.

**returns:** dojo/promise/Promise

**********************************************

<h3 id="remove">remove( id )</h3>
> Deletes an object by its identity.

**_id:_** String | Number
> The identity to use to delete the object

**********************************************

<h3 id="removeparent">removeParent( child, parents )</h3>
> Remove parent(s) from the list of parents of child.

**_child:_** Object
> Store object from which the parent(s) are to be removed.

**_parents:_** (Object | Id) []?
> Object or id or an array of objects or ids to be removed from the parent(s) of child.

**returns:** Boolean
> true if the parent(s) are successfully removed otherwise false.







<h2 id="store-directives">Store Directives</h2>
Store Directives are JavaScript objects whose key:value pairs serve as additional parameters to store
operations. In general, directive objects are passed to store functions as an optional argument.

<h2 id="LoadDirectives"></h2>
### LoadDirectives
> Directives passed to the load() method.

**_all:_** Boolean?

> Indicates if all available data should be loaded. If set to `true`, overrides
> the *filter* property.

**_data:_** any?
> Identifies the data to be loaded.

**_filter:_** (Object | Function)?
> If specified, the objects that match the filter are loaded into the store.
> If filter is a key:value pairs object, each key:value pair is matched with
> the corresponding key:value pair of the store data being loaded. If filter
> is a function, the function is called once for every object to be loaded as: 
> `func( object )`. The function must return either `true` or `false`.

**_handleAs:_** String?

> Specifies how to interpret the payload returned in a server response
> or the data passed to a store method responsible for populating the
> store.

**_url:_** String?

> The Universal Resource Location (URL) to retrieve the store data from.




<h2 id="PutDirectives"></h2>
### PutDirectives
> Directives passed to put() and add() functions for guiding the update and
> creation of store objects. PutDirectives is a JavaScript key:value pairs
> object with the following optional properties:

**_id:_** (String | Number)?
> Indicates the identity of the object if a new object is created.

**_before:_** (Object |  id)?
> If the collection of objects in the store has a natural ordering, this
> indicates that the created or updated object should be placed before
> the object specified by the value of this property. The property value
> can be an object or id. If omitted or null indicates that the object
> should be last.

**_parent:_** Any?
> If the store is hierarchical this property indicates the new parent(s)
> of the created or updated object. This property value can be an Id or
> object or an array of ids or objects.

**_overwrite:_** Boolean?
> If this is provided as a boolean it indicates that the object should or
> should not overwrite an existing object. A value of true indicates that
> a new object should not be created, instead the operation should update
> an existing object. A value of false indicates that an existing object
> should not be updated, a new object should be created (which is the same
> as an add() operation). When this property is not provided, either an
> update or creation is acceptable.

**Example:**

```javascript
{ parent:"Homer", before:"Bart", overwrite:true }
```


<h2 id="QueryDirectives"></h2>
### QueryDirectives
> Optional object with additional parameters for query results. QueryDirectives
> is a JavaScript key:value pairs object with the following optional properties:

**_sort:_** ([SortDirective](#SortDirective)[] | Function)?
> An array of one or more sort directives. If more than one sort directive
> is provided the sort directives are processed in the order in which they
> appear in the sort array. If a comparison function is specified, the array 
> elements are sorted according to the return value of the comparison function.
> See the JavaScript [Array.sort()](https://developer.mozilla.org/en-US/docs/JavaScript/Reference/Global_Objects/Array/sort)
function for details.

**_start:_** Number?
> The first result to begin iteration on. Start identifies an index number
> in the array of result.

**_count:_** Number?
> The maximum number of results that should be returned. If there are less than
> *count* results all available results are returned.

**_ignoreCase:_** Boolean?
> Match object properties case insensitive. Default is false.

**_cacheOnly:_** Boolean?
> If the store maintains an internal cache and cacheOnly is set to true
> only the cache is searched without fetching any data from an external
> data source like a back-end server. Default is false.

**Example:**

```javascript
{ sort:[{attribute:"directory"}, {attribute:"name", ignoreCase:true}], count: 10 }
```





<h2 id="SortDirective"></h2>
### SortDirective
> An object describing what object property to sort on, and the direction of the sort.

**_property:_** String
> The name of the property to sort on.

**_attribue:_** String (dojo compatibility only)
> The name of the property to sort on. Note: This property is provided for
> dojo/store compatibility only, use _property_ instead.

**_descending:_** Boolean?
> The direction of the sort. Default is false.

**_ignoreCase:_** Boolean?
> Compare attribute/property values case insensitive. Default is false.

**Example:**

```javascript
{ attribute:"name", descending:true, ignoreCase:true }
```
