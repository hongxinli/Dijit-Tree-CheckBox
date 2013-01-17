# Store API

The **cbtree/store/api/Store** API is an extension to the **dojo/store/api/Store** API. Therefore, any store
that exposes the **dojo/store/api/Store** API is by default compliant with this API definition.
The API documentation is devided into three section, the [store properties](#wiki-store-properties)
, [store functions](#wiki-store-functions) and [store directives](#wiki-store-directives)

#### IMPORTANT

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

Please refer to the store specific documentation to see which properties and functions are supported.


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



<h2 id="wiki-store-properties">Store Properties</h2>
Store properties define specific features or characteristics of a store, The property names also
represent properties in the keyword object passed to the store constructor. For example:

	require(["cbtree/store/Memory], function (Memory) {
	  var keywordArgs = {url:"some/path/myData", multiParented:true, defaultProperties:{checked:true}};
	  new Memory( keywordArgs );
	}

#### autoLoad:
> **_TYPE_**: Boolean

> Indicates, when a URL is specified, if the data should be loaded during store
> construction or deferred until the user explicitly calls the load function.
> See the store [load](#wiki-load) function for additional information.

> **_DEFAULT_**: true

#### data:
> **_TYPE_**: Object []

> An array of JavaScript key:value pairs objects to be loaded into the store as the
> initial store content. See also _url_

> **_DEFAULT_**: null

<h4 id="wiki-datahandler">dataHandler:</h4>
> **_TYPE_**: Function | Object

> The data handler for the store data or server response. If _dataHandler_ is an
> key:value pairs object, the object should support the following properties:
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
>		dataHandler: { handler: csvHandler,
>		               options: { fieldNames:["col1", "col2"] }
>		             }
>
> The handler function has the following signature:
>
>		handler( response )

> The response argument is a JavaScript key:value pairs object with a
> "text" and/or "data" property.

> **_DEFAULT_**: null

#### defaultProperties:
> **_TYPE_**: Object

> A JavaScript key:values pairs object whose properties and associated
> values are added to new store objects if such properties are missing
> from a new store object.

> **_DEFAULT_**: null

#### handleAs:
> **_TYPE_**: String

> Specifies how to interpret the payload returned in a server response
> or the data passed to a store method responsible for populating the
> store, if any. (typically the store constructor).

> **_DEFAULT_**: null

#### hierarchical:
> **_TYPE_**: Boolean (read-only)

> Indicates if the store is capable of maintaining an object hierarchy.
> The cbtree Models tests for the presence of this property in order to
> determine if it has to set the parent property of an object or if the
> store will handle it.
> If true, the store MUST implement all logic required to support the
> [PutDirective](#wiki-PutDirectives) "parent" and the store's "parentProperty" property.

> **_DEFAULT_**: false

#### idProperty:
> **_TYPE_**: String | Number

> If the store has a single primary key, this indicates the property to use as the
> identity property. The values of this property should be unique.

> **_DEFAULT_**: undefined

#### multiParented:
> **_TYPE_**: Boolean | String

> Indicates if the store is to support multi-parented objects. Multi Parented
> objects are objects that can have more than one parent.

> **_DEFAULT_**: false

#### parentProperty:
> **_TYPE_**: String

> The property name of an object whose value represents the object's parent id
> or ids.

> **_DEFAULT_**: "parent"

#### queryEngine:
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

#### url:
> **_TYPE_**: String

> The Universal Resource Location (URL) to retrieve the store data from.

> **_DEFAULT_**: null





<h2 id="wiki-store-functions">Store Functions</h2>
As stated above, all store functions are optional and may return a dojo/promise/Promise which then resolves
with the synchronous result of the store function.

<h2 id="wiki-add"></h2>
#### add( object, options? )
>	Add a new object to the store, throws an exception if an object with the same identifier already exists.

**_object:_** Object

> The object to be added to the store.

**_options:_** [PutDirectives](#wiki-PutDirectives)?
> Additional directives for creating objects.

**returns:** id


<h2 id="wiki-addParent"></h2>
#### addParent( child, parents )
> Add parent or parents to the list of parents of child.

**_child:_** Object
> Store object to which the parent(s) are added.

**_parents:_** (Object | Id) []?
> Object or id or an array of objects or ids to be added as the parent(s) of child.

**returns:** Boolean
> true if parents were successfully added otherwise false.


<h2 id="wiki-destroy"></h2>
#### destroy()
> Release all memory and mark store as destroyed.


<h2 id="wiki-get"></h2>
#### get( id )
> Retrieves an object by its identity

**_id:_** String | Number

**returns:** Object | void
> The object found otherwise void.




<h2 id="wiki-getChildren"></h2>
#### getChildren( parent, options? )
> Retrieves the children of an object.

**_parent:_** Object
> The object to retrieve the children for.

**_options:_** [QueryDirectives](#wiki-QueryDirectives)?
> Additional options to apply to the retrieval of the children.

**returns:** dojo/store/api/Store.QueryResults
> Note: An empty _QueryResults_ may indicate the parent does not have children or
> the parent itself was not found in the store.




<h2 id="wiki-getIdentity"></h2>
#### getIdentity( object )
> Returns an object's identity. See also the store property "idProperty"

**_object:_** Object
> The object to get the identity from.

**returns:** id | void


<h2 id="wiki-getParents"></h2>
#### getParents( child )
> Retrieve the parent(s) of a store object.

**_child:_** Object
> The store object to retrieve the parent(s) for.

**returns:** Object[] | void






<h2 id="wiki-hasChildren"></h2>
#### hasChildren( parent )
> Returns boolean true if a parent object has known children otherwise false.

**_parent:_** Object
> The object to evaluate.

**returns:** Boolean
> true if the parent object has known children otherwise false.



<h2 id="wiki-isItem"></h2>
#### isItem( object )
> Evaluate if an object is a valid member of this store, that is, it came from this store
> instance. This method MUST execute synchronously.

**_object:_** Object
> Object to evaluate

**returns:** Boolean
> true if the object is a member of this store otherwise false.




<h2 id="wiki-load"></h2>
#### load( options? )
> load the store data from a URL.

**_options:_** [LoadDirectives](#wiki-LoadDirectives)?
> Optional load directives.

**returns:** dojo/promise/Promise
> On successful completion the promise resolves to void. On error the promise is
> rejected with the error condition as its result.





<h2 id="wiki-put"></h2>
#### put( object, options? )
> Stores an object. Throws an exception if an object with the same identification
> already exists **AND** the PutDirective.overwrite is set to false.

**_object:_** Object
> The object to store.

**_options:_** [PutDirectives](#wiki-PutDirectives)?
> Additional directives for storing objects.

**returns:** id



<h2 id="wiki-query"></h2>
#### query( query, options? )
> Queries the store for objects. The query function does not alter the store, but returns a set
> of objects from the store.

**_query:_** String | Object | Function
> The query to use for retrieving objects from the store.

**_options:_** [QueryDirectives](#wiki-QueryDirectives)?
> The optional arguments to apply to the resultset.

**returns:** dojo/store/api/Store.QueryResults
> The results of the query, extended with iterative methods.





<h2 id="wiki-ready"></h2>
#### ready( callback, errback )
> Execute the callback when the store data has been loaded. If an error occurred
> during the loading process errback is called instead.

**_callback:_** Function
> Function called on successful load of the store data.

**_errback:_** Function
> Function called if an error occurred during the store load.





<h2 id="wiki-remove"></h2>
#### remove( id )
> Deletes an object by its identity.

**_id:_** String | Number
> The identity to use to delete the object


<h2 id="wiki-removeParent"></h2>
#### removeParent( child, parents )
> Remove parent(s) from the list of parents of child.

**_child:_** Object
> Store object from which the parent(s) are to be removed.

**_parents:_** (Object | Id) []?
> Object or id or an array of objects or ids to be removed from the parent(s) of child.

**returns:** Boolean
> true if the parent(s) are successfully removed otherwise false.





<h2 id="wiki-store-directives">Store Directives</h2>
Store Directives are JavaScript objects whose key:value pairs serve as additional parameters to store
operations. In general, directive objects are passed to store functions as an optional argument.

<h2 id="wiki-LoadDirectives"></h2>
#### LoadDirectives
> Directives passed to the load() method.

**_all:_** Boolean?

> Indicates if all available data should be loaded.

**_url:_** String?

> The Universal Resource Location (URL) to retrieve the store data from.




<h2 id="wiki-PutDirectives"></h2>
#### PutDirectives
> Directives passed to put() and add() functions for guiding the update and creation
> of store objects. PutDirectives is a JavaScript object with the following optional
> properties:

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

	{ parent:"Homer", before:"Bart", overwrite:true }



<h2 id="wiki-QueryDirectives">QueryDirectives</h2>
#### QueryDirectives
> Optional object with additional parameters for query results.

**_sort:_** ([SortDirective](#wiki-SortDirective)[] | Function)?
> An array of one or more sort directives. If more than one sort directive
> is provided the sort directives are processed in the order in which they
> appear in the sort array. If a compare function is specified, the array 
> elements are sorted according to the return value of the compare function.
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

	{ sort:[{attribute:"directory"}, {attribute:"name", ignoreCase:true}], count: 10 }


<h2 id="wiki-SortDirective">SortDirective</h2>
#### SortDirective
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

	{ attribute:"name", descending:true, ignoreCase:true }

