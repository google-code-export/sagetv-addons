Starting with v2.36, the web UI will now look for and inject additions to the menu at runtime.

The web UI will look for files named `web.menu` in the **defined resource path of installed plugins only.**  When such a file is found, the contents are parsed and injected into the menu.

There is no validation of the content of these files, the content is blindly injected so if you create invalid html then you will break the menu.  Be sure to test injection files before publishing them to the repository.

The format of the file is as follows:

```
!!menuRoot
   <li id="myMenu"><a href="#">Title</a>
     <ul>
        <li><a href="http://go.here/now">Item 1</a></li>
        <li><a href="http://go.here/now">Item 2</a></li>
        <li><a href="http://go.here/now">Item 3</a></li>
     </ul>
   </li>

!!menuGuideItems
   <li><a href="http://somewhere.foo">Item 1</a></li>

```

The format, therefore, is a line starting with `!!` followed by an id from the base menu.  This is the id that you will be injecting into.  The content to be injected is every line following this mark until another line starting with `!!` is encountered.  The first block shows an example of how to inject a new top level menu, the second example shows how to inject an item into an existing menu.  Mutiple injections can be defined in a single file.  The ids you can inject to can be discovered by visiting `http://your_jetty/sage/menu.groovy` on your web ui server and then inspecting the ids shown in that html.