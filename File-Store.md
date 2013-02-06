The cbtree File Store implements an in-memory store whose content represent the
file system layout of the HTTP back-end server document root directory or portions 
thereof. The File Store consist of two components, the dojo client side 
application **cbtree/store/FileStore** and the HTTP back-end server aplication **cbtree/store/server/PHP/cbtreeFileStore.php** or **cbtree/store/server/CGI/bin/cbtreeFileStore.cgi**,
and is dynamically loaded by issueing HTTP GET requests to the back-end server
serving the active HTML page.

Please note that the HTTP GET, DELETE and POST methods are supported but only 
GET is enabled by default. See the Server Side Application 
[configuration](#server-side-configuration) for details.

<h3>Content <span class="mega-icon mega-icon-readme"></span></h3>
* [File Store Requirements](#file-store-requirements)
* [Server Side Applications](#server-side-applications)
* [Server Side Configuration](#server-side-configuration)
* [File Store Security](#file-store-security)
* [File Store Properties](#file-store-properties)
* [File Store API](#file-store-api)
* [Fancy Tree Styling](#fancy-tree-styling)
* [Sample Application](#sample-application)

#### Lazy Store Loading

The cbtree File Store fully supports the concept of *Lazy Loading* which is the
process of loading back-end information on-demand, or in other words: only load
what and when needed.
Depending on the store model used with the File Store the user can influence
this behavior. For example, if the File Store is used with the cbtree 
FileStoreModel, the [model properties](Store-Models#store-model-properties) 
**_checkedStrict_** actually determine how data is retrieved from the back-end
server.

If you elect to use a store model that requires a full store load (no lazy loading), 
such as the [FileStoreModel](Store-Models#wiki-file-store-model) with the model
property *checkedStrict* set, please check the '*Which Application to use*'
section of the [Server Side Applications](#server-side-applications) as
performance may become an issue.


<h2 id="file-store-requirements">File Store Requirements</h2>

In order for the cbtree File Store to function properly your back-end server 
must host at least one of the server side applications included in the cbtree 
package:

* cbtreeFileStore.php
* cbtreeFileStore.cgi

See the [Server Side Application](#server-side-applications) section for details
on how to select and configure the correct application for your environment and
possible additional requirements. The PHP implementation has been fully tested
with PHP 5.3.1

#### File Store Restrictions

File Store (client side) use the JavaScript XHR API to communicate with the back-end
server therefore cross-domain access is, by default, denied. If you need to retrieve file
system information from any server other than the one hosting the active HTML
page you must configure a so-called HTTP proxy server. (**HTTP server configuration is beyond the
scope of this document**).


<h2 id="server-side-applications">Server Side Applications</h2>

The cbtree File Store comes with two implementations of the cbtree Server Side Application,
one written in PHP the other is an ANSI-C CGI application compliant with the 
[CGI Version 1.1](http://datatracker.ietf.org/doc/rfc3875/) specification. Your HTTP 
server must host at least one of them. The following sections describe how to select the 
appropriate Server Side Application for your environment, the server requirements and 
optionally how to configure the application environment.

#### Which Application to use

Both applications offer the same functionality but they operates in a different HTTP 
back-end server environment each with its own requirements.  
The primary selection criteria are:

1. What application environment does your server support? PHP, CGI or both.
2. Is a complete (initial) store load required by you application?
3. The size of the file system you want to expose.

If your server only support PHP or CGI but not both the choice is simple. If, on the other hand, 
both are supported and your application requires a full store load, that is, load all available
information up-front like with the FileStoreModel that has strict parent-child relationship enabled, 
than the last question will determine the final outcome. If you operate on a large file system with
10,000+ files it is highly recommended you use the ANSI-C CGI implementation.

Most scripting languages such as PHP are implemented as an interpreter and therefore slower than
any native compiled code like the ANSI-C CGI application. As an example, loading the entire store
running both the browser application and the PHP server side aplication on the same 4 core 2.4 Mhz AMD
processor with a file system of 21,000 files takes about 6-7 seconds. Running the exact same browser
application on the same platform but with the CGI application takes 3-4 seconds.

If your application does ***NOT*** require a full store load (lazy loading is sufficient) and none
of the directories served by the Server Side Application has hundreds of file entries you probably
won't notice much of a difference as only a relatively small amounts of processing power is
required to serve each client request.

#### cbtreeFileStore.php

If you are planning on using the PHP implementation, your HTTP server must provide PHP support and have the
PHP JSON feature set enabled. The actual location of the server application on your back-end
server is irrelevant as long as it can be access using a formal URL. See the usage of the 
store property *baseURL* and the environment variable CBTREE_BASEPATH for more details.

#### cbtreeFileStore.cgi

The ANSI-C CGI application needs to be compiled for the target Operating System. 
Currently a Microsoft Windows implementation and associated Visual Studio 2008 project
is included in the cbtree package.
If you need the CGI application for another OS please refer to the inline documentation
of the `cbtree_NP.c` module for details. Module `cbtree_NP.c` is the only source module
that contains Operating System specific code.

The location to install the CGI application depends on the HTTP server configuration.
On Apache HTTP servers the application is typically installed in the `/cgi-bin` directory which,
if configurred properly, is outside your document root.
For Apache users, please refer to the [CGI configuration](http://httpd.apache.org/docs/2.2/howto/cgi.html)
instructions for details.

#### Write your own application

If, for whatever reason, you have to or want to write your own server side application use 
the source code of the PHP and ANSI-C implementation as a functional guideline.
Below you'll find the ABNF notation for the server request and response.

##### Request:

```
HTTP-GET      = uri ["?" query-string]
query-string  = qs-param *("&" qs-param)
qs-param      = basePath / path / query / queryOptions / options / 
                start / count / sort
authToken     = "authToken" "=" json-object
basePath      = "basePath" "=" path-rfc3986
path          = "path" "=" path-rfc3986
query         = "query" "=" json-object
query-options = "queryOptions" "=" json-object
options       = "options" "=" json-array
start         = "start" "=" number
count         = "count" "=" number
sort          = "sort" "=" json-array
```

##### Response:

```
response      = "{" (totals ",")? (status ",")? (identifier ",")? (label ",")? file-list "}"
totals        = ""total"" ":" number
status        = ""status"" ":" status-code
status-code   =    "200" / "204" / "401"
identifier    = ""identifier"" ":" quoted-string
label         = ""label"" ":" quoted-string
file-list     = ""items"" ":" "[" file-info* "]"
file-info     = "{" name "," path "," size "," modified ("," icon)? "," directory 
                    ("," children "," expanded)? "}"
name          = ""name"" ":" json-string
path          = ""path"" ":" json-string
size          = ""size"" ":" number
modified      = ""modified"" ":" number
icon          = ""icon"" ":" classname-string
directory     = ""directory"" ":" ("true" / "false")
children      = "[" file-info* "]"
expanded      = ""_EX"" ":" ("true" / "false")
quoted-string = """ CHAR* """
number        = DIGIT+
DIGIT         = "0" / "1" / "2" / "3" / "4" / "5" / "6" / "7" / "8" / "9"
```

Please refer to [http://json.org/](http://json.org/) for the proper JSON encoding rules.


<h2 id="server-side-configuration">Server Side Configuration</h2>

The Server Side Application utilizes two optional environment variables to control which HTTP request
types to support and to set a system wide basepath for the application:

CBTREE_BASEPATH

> The basePath is a URI reference (rfc 3986) relative to the server's document root used to
> compose the root directory.  If this variable is set it overwrites the basePath parameter
> in any HTTP query string and therefore becomes the server wide basepath.

    CBTREE_BASEPATH /system/wide/path

> Given the above basepath and if the document root of your server is `/MyServer/htdocs` the root
> directory for the Server Side Application becomes: `/MyServer/htdocs/system/wide/path`

CBTREE_METHODS

> A comma separated list of HTTP methods to be supported by the Server Side Application. 
> By default only HTTP GET is supported. Possible options are uppercase GET, DELETE and POST. Example:

    CBTREE_METHODS GET,DELETE,POST

> If only HTTP GET support is required there is no need to define `CBTREE_METHODS`, if however, 
> HTTP DELETE and/or POST support is required you ***MUST*** define the `CBTREE_METHODS` variable.
> HTTP POST is required to rename files.

#### IMPORTANT

Some HTTP servers require  special configuration to make environment variables available to
script or CGI application.  For example, the Apache HTTP server requires you to either use
the *SetEnv* or *PassEnv* directive. To make environment variable `CBTREE_METHODS`
available add the following to your httpd.conf file:

    SetEnv CBTREE_METHODS GET,DELETE

                or

    PassEnv CBTREE_METHODS

Please refer to the Apache [mod_env](http://httpd.apache.org/docs/2.2/mod/mod_env.html) section
for additional details.

<span class="mega-icon mega-icon-exclamation"></span>
Whenever you set or change the value of the environment variables you ***MUST*** restart you HTTP
server to make these values available to scripts and CGI applications.


<h2 id="file-store-security">File Store Security</h2>

As with any application exposed to the Internet there are security issues you need to consider.
Both the PHP and CGI Server Side Application perform strict parameter checking in that any malformed
parameter is rejected, not just skipped, resulting in a HTTP 400 (Bad Request) error response. Any attempt to access
files outside the root directory results in a HTTP 403 (Forbidden) response.

By default only HTTP GET requests are accepted, if you want to support HTTP DELETE and/or POST you ***MUST***
set the server side environment variable CBTREE_METHODS. See [Server Side Configuration](#server-side-configuration)
for details. When HTTP POST is enabled only renaming files is supported, you cannot upload/modify files or other content.

#### Authentication

The Server Side Applications ***DO NOT*** perform any authentication. The client side can however pass
a so-called authentication token allowing you to implement you own authentication if needed.

#### File Access Restrictions

Neither Server Side Application will process any HTTP server directives. Any file and/or 
directory access restrictions in files like .htaccess are ignored by the Server Side Application. 
However, such file access restrictions will still be enforced by the HTTP server when users try to
access the files and/or directories directly. It is therefore highly recommended to put those types
of restrictions in place anyway as part of your standard security procedures.

If you have to rely on the HTTP server security, any HTTP request must be evaluated ***BEFORE***
the Server Side Application is invoked.
In addition do not rely on Operating System specific access privilages as PHP may not recognize
such features. For example, PHP does not recognize the Micorsoft Windows *hidden* file attribute.
In general, only expose files and directories that are intended for public consumption.
(See also *Hiding Files*)

#### Hiding Files

The Server Side Applications will recognize any file whose name starts with a dot(.) as a 'hidden' file
and exclude such files from being included in a response unless the *showHiddenFiles* option of the
File Store is set. In general, it is good pratice not to include any files you may consider private, 
hidden or not, in any directory you expose to the outside world.

<span class="mega-icon mega-icon-exclamation"></span>
Only the Windwos CGI implementation will also recognize the Microsoft Windows hidden file attribute. 

<h2 id="file-store-properties">File Store Properties</h2>

This section describes the properties of the cbtree File Store. File Store 
properties define specific features or characteristics of the store,
The property names also represent properties in the keyword object passed to
the store constructor. For example:

```javascript
require(["cbtree/store/FileStore"], function (FileStore) {
  var keywordArgs = { url: "../../store/server/PHP/cbtreeFileStore.php", 
                      basePath:"./", 
                      options:["iconClass"],
                      sort: [ {attribute:"directory"}, {attribute:"name", ignoreCase: true} ] }
  var store = new FileStore( keywordArgs );
}
```


#### autoLoad:
> **_TYPE_**: Boolean

> If set to true, the File Store is loaded immediately when the store is created 
> otherwise the store load is deferred until the user explicitly calls the load
> function. See the store [load](Store-API#wiki-load) function for additional
> information.

> **_DEFAULT_**: false

#### authToken:
> **_TYPE_**: Object

> An arbitrary JavaScript object which is passed to the back-end server with each XHR call.
> The File Store client does not put any restrictions on the content of the object.

> **_DEFAULT_**: null

#### basePath:
> **_TYPE_**: String

> The basePath property is a URI reference (rfc 3986) relative to the
> server's document root and used by the server side application to compose the root 
> directory, as a result the root directory is defined as:
>
> root-dir = document-root "/" basepath
>
> <span class="mega-icon mega-icon-exclamation"></span>
> If the environment variable CBTREE_BASEPATH is set on the HTTP server this
> property is ignored.

> **_DEFAULT_**: "./"

#### defaultProperties:
> **_TYPE_**: Object
> A JavaScript key:values pairs object whose properties are mixed in with any
> new store object.

> **_DEFAULT_**: null

#### options:
> **_TYPE_**: String | String[]

> A string of comma separated keywords or an array of keyword strings. Some of
> the keywords are passed to the server side application and influence the 
> server response while others are used on the client side query.
> The following keywords are currently supported:

> #### dirsOnly:
> <span style="margin-left:20px;">Return only directory entries.</span>
> #### iconClass
> <span style="margin-left:20px;">Include the css classname for files and 
		directories in the response. See *Fancy Tree Styling* below.</span>
> #### showHiddenFiles
> <span style="margin-left:20px;">Include hidden files in the response. 
(see *Hidden Files* above).</span>

> **_DEFAULT_**: [] (empty array)

#### sort:
> **_TYPE:_** [SortDirective](Store-API#wiki-SortDirective)[] | Function

> An array of one or more sort directives. If more than one sort directive
> is provided the sort directives are processed in the order in which they
> appear in the sort array. If a compare function is specified, the array 
> elements are sorted according to the return value of the compare function.  
>
> If this property is set it will be used with every store query.
> See the JavaScript [Array.sort()](https://developer.mozilla.org/en-US/docs/JavaScript/Reference/Global_Objects/Array/sort)
function for details.

> **_DEFAULT_**: [] (empty array)

#### queryEngine:
> **_TYPE_**: Function

> This property defines the query engine to use for querying the store data.
> The signature of the queryEngine function is as follows:
>
>		queryEngineFunction( queryObject, queryOptions? )
>
> The queryEngine function MUST return a function that can execute the provided 
> query on an array of objects. For example:

>		var query = store.queryEngine( {foo:"bar"}, {count:10} );
>		query(someArray) -> filtered array

> The returned query function may have a "matches" property that can be
> used to determine if an object matches the query. For example:

>		query.matches( {id:"some-object", foo:"bar"} ) -> true
>		query.matches( {id:"some-object", foo:"something else"} ) -> false

> **_DEFAULT_**: cbtree/store/util/QueryEngine

#### url:
> **_TYPE_**: String

> The public URL of the Server Side Application serving the File Store. For
> example: **http://MyServer/cgi-bin/cbtreeFileStore.cgi**

> **_DEFAULT_**: ""





<h2 id="file-store-api">File Store API</h2>

#### get( id, storeOnly? )
> Retrieves an object by its identity

**_id:_** String | Number

**_storeOnly:_** Boolean

**returns:** Object | void
> The object found otherwise void.


**********************************************
#### getChildren( parent, options? )
> Retrieves the children of an object.

**_parent:_** Object
> The object to retrieve the children for.

**_options:_** [QueryDirectives](Store-API#wiki-QueryDirectives)?
> Additional options to apply to the retrieval of the children.

**returns:** dojo/store/api/Store.QueryResults
> Note: An empty _QueryResults_ may indicate the parent does not have children or
> the parent itself was not found in the store.


*********************************************
#### getIdentity( item )
> Returns a unique identifier for an item. The default identifier for the File Store 
> is the attribute *path* unless otherwise specified by the back-end server. The return
> value will be either a string or something that has a toString() method.

*item:* store.item
> A valid file.store item.

**********************************************
#### hasChildren( parent )
> Returns boolean true if a parent object has known children otherwise false.

**_parent:_** Object
> The object to evaluate.

**returns:** Boolean
> true if the parent object has known children otherwise false.

*********************************************
#### isItem( something )
> Returns true if *something* is an item and came from the store instance. Returns
> false if *something* is a literal, an item from another store instance, or is any
> object other than an item.

*something:* AnyType
> Can be anything.

**returns:** Boolean
> Returns true if 'something' is a store object otherwise false.

**********************************************
#### load( options? )
> Initiate store load.

**_options:_** [LoadDirectives](#LoadDirectives)?
> Optional load directives.

**returns:** dojo/promise/Promise
> On successful completion the promise resolves to void. On error the promise is
> rejected with the error condition as its result.


**********************************************
#### put( object, options? )
> Stores an object. Throws an exception if an object with the same identification
> already exists **AND** the PutDirective.overwrite is set to false.

**_object:_** Object
> The object to store.

**_options:_** [PutDirectives](#PutDirectives)?
> Additional directives for storing objects.

**returns:** id

**********************************************
#### query( query, options? )
> Queries the store for objects. The query function does not alter the store, 
> but returns a set of objects from the store.

**_query:_** String | Object | Function
> The query to use for retrieving objects from the store. See the [Query Engine](Query-Engine)
> for a detailed description of the [query argument](Query-Engine#the-query-argument).

**_options:_** [QueryDirectives](#QueryDirectives)?
> The optional arguments to apply to the resultset.

**returns:** dojo/store/api/Store.QueryResults
> The results of the query, extended with iterative methods.



**********************************************
#### ready( callback, errback )
> Execute the callback when the store data has been loaded. If an error occurred
> during the loading process errback is called instead.

**_callback:_** Function
> Function called on successful load of the store data.

**_errback:_** Function
> Function called if an error occurred during the store load.

**returns:** void



**********************************************
#### remove( id )
> Deletes an object by its identity.

**_id:_** String | Number
> The identity to use to delete the object

**returns:** dojo/promise/Promise


**********************************************
#### rename( object, newPath)
> Rename an object

**_object:_** Object
> The object to store.

**_newPath:_** String



<h2 id="fancy-tree-styling">Fancy Tree Styling</h2>

The File Store supports the option **_iconClass_** which will force it to include
a CSS icon classname for each store item (file). The classname is based on the
file extension.
If the *iconClass* option is set, the file store will generate a css classname
which is formatted according to the follows ABNF notation:

    iconClassname = ("fileIcon" fileExtension) SP "fileIcon"
    fileExtention = string
		
As a result, each store item will have an additional property called *icon* 
whose value is a pair of camelCase classnames. The first character of fileExtension
is always uppercase, all other characters are lowercase like in *fileIconXml*. 
The only exception is a file system directory which gets the classname *fileIconDIR* to 
distinguesh between, although not common, a file with the extension '.dir'.  

The first classname is followed by a whitespace and the generic classname 
"fileIcon". The generic classname is used as a fallback in case you don't have
a CSS definition for the classname with the file extension. Therefore
always make sure you at least have a CSS definition for "fileIcon".

### Predefined Icons ###
The CheckBox Tree package comes with two sets of predefined icons and associated
CSS definitions. One set is based on the Apache HTTP server 'Fancy Index' icons
the other is a set of Microsoft Windows explorer icons.
The css definitions for these icon sets must be loaded explicitly, load either
`cbtree/icons/fileIconsApache.css` ***OR*** `cbtree/icons/fileIconsMS.css` but
***NOT BOTH***.

    <link rel="stylesheet" href="/js/dojotoolkit/cbtree/icons/fileIconsMS.css" />

The icon sprites and CCS definitions included in the package serve as an example
only, they certainly do not cover all possible file extensions. Also the File
Store only looks at the file extension when generating the classname and does
not look at the files content type.

### Prerequisites ###
To enable and use the Fancy Tree Styling in your applications the following 
requirements must be met:

* The Tree Styling extension must have been loaded. (`cbtree/extensions/Styling`)
* A set of icons or an icon sprite must be available.
* A CSS definitions file must be available and loaded.
* The FileStoreModel and optionally the tree must be configured for icon support.

At the end of this document you can find a complete example of an application 
using the *Fancy Tree Styling*.  
See the [Tree Styling](Tree-Styling) documentation for additional information.





<h2 id="sample-application">Sample Application</h2>

The following sample application shows the document root directory of the back-end server
as a simple tree with checkboxes. Notice that because the model property *checkedStrict* 
is disabled the FileStoreModel will automatically apply lazy loading.

```html
<!DOCTYPE html>
<html>
  <head>
    <meta charset="utf-8">
    <title>The CheckBox Tree and a cbtree/store FileStore</title>
    <style type="text/css">
      @import "../../../dijit/themes/claro/claro.css";
      @import "../../themes/claro/claro.css";
    </style>

    <script type="text/javascript">
      var dojoConfig = {
            async: true,
            parseOnLoad: true,
            isDebug: false,
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
        "cbtree/Tree",                  // Checkbox tree
        "cbtree/model/FileStoreModel",
        "cbtree/store/FileStore"
        ], function (ready, Tree, FileStoreModel, FileStore) {
        
           // Because we don't have knowledge of the file system layout of the HTTP server
           // the 'basePath' is set to the document root.

          var store = new FileStore( { url: "../../store/server/PHP/cbtreeFileStore.php",
                                       basePath:"./",
                                       sort: [{attribute:"directory"},{attribute:"name", ignoreCase: true}]
                                     } );
          var model = new FileStoreModel( {
                  store: store,
                  rootLabel: 'My HTTP Document Root',
                  checkedRoot: true,
                  checkedStrict: false
               });

          ready(function() {
            var tree = new Tree( { model: model, id: "MenuTree", persist:false }, "CheckboxTree" );
            tree.startup();
          });
        });
    </script>
  </head>
  <body class="claro">
    <h1 class="DemoTitle">The CheckBox Tree with a dojo/store File Store</h1>
    <div id="CheckboxTree">
    </div>
  </body>
</html>
```




### Fancy Tree Styling ###

The following sample applies *Fancy Icons* to the tree. Notice that in addition 
to the regular css files we also import the css file `"../../icons/fileIconsApache.css"`
which holds the Apache icon definitions.

To enable support for the custom Apache icons we set the File Store options 
*iconClass* which forces the store to add the "icon" property to each 
store object and finally we set the File Store Model property *iconAttr*.

```html
<!DOCTYPE html>
<html>
  <head>
    <meta charset="utf-8">
    <title>The CheckBox Tree and a cbtree/store FileStore</title>
    <!--   
      Load the CSS files including the Apache style icons, alternatively load fileIconsMS.css 
      instead to get Microsoft Windows style icons (but not both).
    -->
    <style type="text/css">
      @import "../../../dijit/themes/claro/claro.css";
      @import "../../themes/claro/claro.css";
      @import "../../icons/fileIconsApache.css";
    </style>

    <script type="text/javascript">
      var dojoConfig = {
            async: true,
            parseOnLoad: true,
            isDebug: false,
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
        "cbtree/Tree",                  // Checkbox tree
        "cbtree/store/FileStore",       // File Store
        "cbtree/model/FileStoreModel",  // File Store Model
        "cbtree/extensions/Styling"     // Tree Styling extension
        ], function (ready, Tree, FileStore, FileStoreModel, TreeStyling ) {

          var store = new FileStore( { url: "../../store/server/PHP/cbtreeFileStore.php",
                                       basePath:"./",
                                       options:["iconClass"],
                                       sort: [{attribute:"directory"},{attribute:"name", ignoreCase: true}]
                                     } );
          var model = new FileStoreModel( {
                  store: store,
                  rootLabel: 'My HTTP Document Root',
                  checkedRoot: true,
                  checkedStrict: "inherit",
                  iconAttr: "icon"
               });

          ready(function() {
            var tree = new Tree( { model: model, id: "MenuTree", persist:false }, "CheckboxTree" );
            tree.startup();
          });
        });
    </script>

  </head>

  <body class="claro">
    <h1 class="DemoTitle">The CheckBox Tree with a dojo/store File Store</h1>
    <div id="CheckboxTree">
    </div>
  </body>
</html>
```

