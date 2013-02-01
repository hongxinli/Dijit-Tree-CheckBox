# Installation

Before installing the cbtree package you must first install the latest [Dojo Toolkit](http://dojotoolkit.org/download/), that is, dojo 1.8 or
later. Assuming you created a directory structure like:

    /dojotoolkit
      /dijit
      /dojo
      /util

Unzip the cbtree package in the '/dojotoolkit' directory which will create
a new '/cbtree' directory like:

    /dojotoolkit
      /cbtree
      /dijit
      /dojo
      /util

The correct installation location for /cbtree is important in order for the 
included demos to work properly as all demos use relative paths to load dojo
and dijit modules.

#### CheckBox Tree Directories
After installation the /cbtree directory should have the following subdirectories:

    /cbtree
      /data           (deprecated)
      /demos
      /documentation  (deprecated)
      /icons
      /model
      /models         (deprecated)
      /store
      /templates
      /themes
      /util
      
`/data`
> The <code>/data</code> directory holds the cbtree stores implementing the 
> dojo/data/api API's. Be adviced, these stores have been deprecated and will
> removed in the future. See the /store directory instead.

`/demos`
> The /demos directory holds two subdirectories, /data and /store. The /data
> subdirectory holds demos using the deprecated dojo/data/ItemFile stores and 
related models.  
> The /store subdirectory holds demos using the new cbtree/store Stores.

`/documentation`
> The /documentation directory holds all CheckBox Tree documentation related to
> previous cbtree release. Starting with cbtree release v0.9.3-0 this wiki will
> serves as the online documentation. Be adviced, these documents will be removed
> in the future.

`/icons`
> The /icons directory holds sample custom icons and their associated css files,
> please refer to [Tree Styling](Tree-Styling) for a detailed description
> on how to use custom icons.


`/model`


`/models`
> The /models directory holds the cbtree store models for used with the legacy
> dojo/data/ItemFile stores. Be adviced, these models have been deprecated and
> will removed in the future. See the /model directory instead.

`/store`
> The /store directory holds the new cbtree stores, all implementing the new
> cbtree/store API. The cbtree/store API is an extension to the dojo/store/api/Store API.

`/templates`
> The /templates directory holds the HTML template file for the CheckBox Tree.

`/themes`
> The /themes directory holds the css files and images required for each dijit
> theme, that is, claro, nihilo, soria and tundra.

`/util`
