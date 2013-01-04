# Store API

The **cbtree/store/api/Store** API is an extension to the **dojo/store/api/Store** API. Therefore, any store
that exposes the **dojo/store/api/Store** API is by default compliant with this API definition.
The API documentation is devided into three section, the [store properties](#store-properties)
, [store functions](#store-functions) and [store directives](#store-directives)

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
			<td>JavaScript key:value pairs object (hash)</td>
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

#### IMPORTANT

This API defines function signatures ONLY without providing implementation details.
All functions and properties defined in this API are optional and require implemention
**ONLY** if the store is to offer the functionality. Therefore, your store of choice may
only support a subset of this API.  
Please refer to the store specific documentation to see which properties and what feature
set is supported.

<h2 id="store-properties">Store Properties</h2>
Store properties define specific features or characteristics of a store, The property names also
represent properties in the keyword object passed to the store constructor. For example:

	require(["cbtree/store/Memory], function (Memory) {
	  var keywordArgs = {url:"some/path/myData", multiParented:true, defaultProperties:{checked:true}};
	  new Memory( keywordArgs );
	}

#### data:
> **_TYPE_**: Object []

> An array of JavaScript key:value pairs objects to be loaded into the store as the
> initial store content. See also _url_

> **_DEFAULT_**: null

#### dataHandler:
> **_TYPE_**: Function | Object

> The data handler for the store data or server response. If _dataHandler_ is an
> key:value pairs object, the object should looks like:
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
> "text" or "data" property.

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
> Put.Directive "parent" and the store's "parentProperty" property.  

> **_DEFAULT_**: false

#### idProperty:
> **_TYPE_**: String | Number

> If the store has a single primary key, this indicates the property to use as the
> identity property. The values of this property should be unique.

> **_DEFAULT_**: undefined

#### multiParented:
> **_TYPE_**: Boolean

> Indicates if the store supports multi-parented objects. Multi Parented
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
> defines the query engine to use for querying the data store.
> The queryEngine function takes a query and query options and returns a function
> that can execute the provided query on a JavaScript array of objects.

>		var query = store.queryEngine({foo:"bar"}, {count:10});
>		query(someArray) -> filtered array

> The returned query function may have a "matches" property that can be
> used to determine if an object matches the query. For example:

>		query.matches({id:"some-object", foo:"bar"}) -> true
>		query.matches({id:"some-object", foo:"something else"}) -> false

> **_DEFAULT_**: cbtree/store/util/QueryEngine

#### url:
> **_TYPE_**: String

> The Universal Resource Location (URL) to retrieve the store data from.

> **_DEFAULT_**: null





<h2 id="store-functions">Store Functions</h2>

#### add( object, options )
> Add a new object to the store, throws an exception if an object with the same identifier already exists.

> **_object:_** Object

> **_options:_** PutDirectives

> **returns:**: id



#### addParent( child, parents )
> Add parent or parents to the list of parents of child.

**_child:_** Object
> Store object to which the parent(s) are added.

**_parents:_** Object []? | Id []?
> Object or id or an array of objects or ids to be added as the parent(s) of child.



#### destroy()
> Release all memory and mark store as destroyed.


#### get( id )
> Retrieves an object by its identity

**_id:_** String | Number

#### getChildren( parent, options )

**_parent:_** Object

**_options:_** QueryDirectives



#### getIdentity( object )
> Returns an object's identity

**_object:_** Object
> The object to get the identity from



#### getParents( child )

**_child:_** Object


#### hasChildren( parent )

> Returns boolean true if a parent object has known children otherwise false.

**_parent:_** Object

#### isItem( object )

**_object:_** Object


#### load( options )

**_options:_** LoadDirectives



#### put( object, options )

**_object:_** Object

**_options:_** PutDirectives


#### query( query, options )

**_query:_** Object

**_options:_** QueryDirectives



#### ready( callback, errback )

**_callback:_** Function

**_errback:_** Function


#### remove( id )

**_id:_** String | Number



#### removeParent( child, parents )

**_child:_** Object

**_parents:_** Object []? | Id []?
> Object or id or an array of objects or ids to be removed from the parent(s) of child.


<h2 id="store-directives">Store Directives</h2>
