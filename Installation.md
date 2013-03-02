Before installing the cbtree package you must first install the latest [Dojo Toolkit](http://dojotoolkit.org/download/), that is, dojo 1.8 or
later. Assuming you created a directory structure like:

    /dojotoolkit
      /dijit
      /dojo
      /util

Unzip the cbtree package in the `/dojotoolkit` directory which will create
a new `/cbtree` directory like:

    /dojotoolkit
      /cbtree
      /dijit
      /dojo
      /util

<span class="mega-icon mega-icon-exclamation"></span>
The location of the `/cbtree` directory is important in order for the demos included
in the package to work correctly. All demos use relative paths to load dojo and
dijit modules. If you are not interested in running any of the demos you can
install the cbtree package at any location.

### CheckBox Tree Directories
After installation the `/cbtree` directory will have the following subdirectories:

    /cbtree
      /data           (deprecated)
      /demos
      /extensions
      /icons
      /model
      /models         (deprecated)
      /store
      /templates
      /themes
      /util

<span class="mega-icon mega-icon-exclamation"></span> Please note that all 
directories marked **_deprecated_** are maintained for backward compatability 
only and will be removed in the future. 

#### /data
> The `/data` directory holds the cbtree stores implementing the legacy
> dojo/data/api API's.  
> <span class="mini-icon mini-icon-exclamation"></span> These stores have been
> deprecated and will removed in the future. See the /store directory instead.

#### /demos
> The `/demos` directory holds two subdirectories, `/data` and `/store`.
> The `/data` subdirectory holds demos using the deprecated dojo/data/ItemFile
> stores and related models. The `/store` subdirectory holds demos using the
> new cbtree/store Stores.

### /extensions
> The /extensions directory holds the optional tree extensions.

#### /icons
> The /icons directory holds sample custom icons and their associated css files,
> please refer to [Tree Styling](Tree-Styling) for a detailed description
> on how to use custom icons.


#### /model
> The /models directory holds the cbtree store models used with the new
> cbtree/store stores located in the /store directory.

#### /models
> The /models directory holds the cbtree store models used with the legacy
> dojo/data/ItemFile stores.  
> <span class="mini-icon mini-icon-exclamation"></span> These models have been
> deprecated and will removed in the future. See the /model directory
> instead.

#### /store
> The /store directory holds the new cbtree stores, all implementing the new
> cbtree/store API. The cbtree/store API is an extension to the dojo/store/api/Store API.

#### /templates
> The /templates directory holds the HTML template file for the CheckBox Tree.

#### /themes
> The /themes directory holds the css files and images required for each dijit
> theme, that is, claro, nihilo, soria and tundra.

#### /util
> Common utilities and extensions.
