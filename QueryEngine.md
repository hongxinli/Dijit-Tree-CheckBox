# The Query Engine

The cbtree **/cbtree/store/util/QueryEngine** provides the search and query functionality
for all cbtree stores. The name "query engine" is somewhat misleading in the sense that it
does NOT actually query the store, instead it returns a function capable of qurying the store.

The query engine prototype/signature looks like:

#### queryEngine( query, queryDirectives? )
> Returns a JavaScript function capable of executing a store query.

**_query:_** Object | Function | String
> The query parameter can be either a JavaScript key:value pairs object, a function or a string:

**_queryDirectives:_** queryDirectives?
> The queryDirectives parameter is a JavaScript key:value pair object providing additional sort
> and pagination details to be applied to the query result. For a detailed description of the
> queryDirective properties, see the store API [queryDirectives](Store-API#queryDirectives).

**returns:** Function
> The function returned has the following signature: **queryFunction( Object[] )**
