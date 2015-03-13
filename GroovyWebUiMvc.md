# Introduction #

A more complete example of how to use the MVC pattern, groovlets, and GSPs to create new web pages in the web UI.

It's assumed you've read over the initial "Hello, World" tutorial, which explains the basics of creating groovlets and GSPs.  With that knowledge in mind, it's now time to do a more complete example using the MVC pattern.

The [MVC](http://en.wikipedia.org/wiki/Model%E2%80%93view%E2%80%93controller) pattern allows us to separate all the logic of our dynamic web page away from the display of the final data.  This serves many purposes, but the biggest reason to do this is maintenance and management of the code.

# The Goal #

Let's create a new web page that lists future airings of a given title.  Is this useful?  Maybe, maybe not, but it will allow me to show all the details of creating a new, functional web page in the UI.

# Breaking Down the MVC Components #

So let's break down our tasks.  At the highest level you basically need to create each component of the MVC pattern.  I'll the details to wikipedia or other materials for reading, and summarize it as this:

**M** odel: Our model is the `wiz.bin`, which we access via the SageTV API using the sagex-api wrappers.  Good news: This is already done for us!  Yes!!

**V** iew: Our view is going to be a web page, written as GSP.  We'll write the view last, after we create the controller.

**C** ontroller: Our controller is going to be a groovy script, accessible via the web UI.  The trick is that we do all our "business" logic in the controller then pass the "business" objects to our view for final display.

When implementing MVC, you usually implement the M then the C then the V.  Well, the M is already done for us, so let's start with the C.

# Creating the Controller #

If you look at the groovy scripts embedded in the web UI war file, you'll see an emerging pattern in the way I implement MVC.  This is not **_the_** way to implement MVC, but it's the way I typically do it.  You'll find other implementations and probably even find commentaries saying my way is wrong. :)  This is **_a_** way to do it.  Read it, learn from it, adopt it as you see fit.

So what do I do?  Simple, really.  Process the input, build my business objects then pass control over to my view to display my built up objects.

In this case, I want to build up a list of Airing objects to be displayed based on the title received as input.  In a full application, you would probably accept input from a form.  We're not going to get that fancy and just expect the user to call our URL with their search term embedded.  Making the input come from a form is an exercise left for the reader.

Create a new file called `title_search.groovy` and put the following in it:

```
import sagex.api.*

if(!params['title']) {
   response.sendError(400, 'You must specify a title parameter in the request!')
} else {
   def airings = Database.Sort(Database.FilterByRange(Database.SearchByTitle(params['title']), 'GetAiringStartTime', System.currentTimeMillis(), System.currentTimeMillis() + 86400000L, true), false, 'GetAiringStartTime')
   request.setAttribute('airings', airings)
   request.setAttribute('pageTitle', "Search Results: ${params['title']}")
   request.getRequestDispatcher('title_search.gsp').forward(request, response)
}
```

What are we doing here?  First, we need to import the sagex APIs so we do that with the import statement.  Next, we check to see if the request includes a title parameter.  If it doesn't we generate an error response back to the browser.  If it does, we use that title to perform a search, filter the results to include only those airings in the next 24 hours, then assign the final list as an attribute of the request.  We also set the special attribute `pageTitle` which will allow us to customize the page title in our view.  Finally, we then forward the request onto our view, which will process the objects we just assigned as attributes.

# Creating the View #

Now let's create our view.  Create a file called `title_search.gsp` and add the following contents:

```
<@ webserver/templates/header.gsp @>
<% import sagex.api.* %>
<p>${new Date()}: Future airings of "${params['title']}"</p>
<ul>
<% request.getAttribute('airings').each { %>
   <li>${new Date(AiringAPI.GetAiringStartTime(it))} on channel ${AiringAPI.GetAiringChannelNumber(it)} (${AiringAPI.GetAiringChannelName(it)})</li>
<% } %>
</ul>
<@ webserver/templates/footer.gsp @>
```

What are we doing here?  We import the sagex APIs, print a header then loop through our list of airings - accessed via the `airings` attribute we set in our controller script - then dump the search results out in an unordered list.  You'll also notice the references to `header.gsp` and `footer.gsp`.  This templates are packaged as part of the web UI plugin and will generate a template HTML page that provides the basic look and feel of the existing web UI web pages.  Remove those lines from the gsp file and reload it.  Now you see what they're for.

# Let's test it out! #

Copy these two files you've created to `\SageTV\webserver\groovy\` on your SageTV server.  Open your web browser and go to:

http://192.168.0.1:8080/sage/title_search.groovy

Replace the IP and port as appropriate.  You should get an error saying:

` You must specify a title parameter in the request! `

That's good because we didn't.  So append the following to the end of the URL above:

`?title=Californication`

Reload the page.  If your EPG has any airings of Californication within the next 24 hours then they should be listed.  If not, replace the title with another show.

# Notes #

## Accessing the GSP Directly ##

What happens if you try to directly load the gsp page?  An exercise would be to handle a direct load and generate an appropriate error message.  The reason you get "null" on the results page is because you're attempting to loop through the list of airings, but they don't exist because the attribute is only set by the controller script.

## Combining the View and the Controller ##

You might be asking, "why are you splitting the view and the controller into separate files?"  That's a good question.  For this particular example, you could easily (and probably correctly) combine the view and the controller into a single gsp file and forget creating a separate groovy script controller.  I just did it for completeness.  Imagine a controller with many different actions and views - such examples exist in the web UI source code (look at the plugin manager).  A more complex application is why you would definitely want to separate the view from the controller.  For the smallest of web pages, combining the view and controller is more than acceptable.  This tutorial would qualify.

## Packaging a Plugin ##

So let's say you wanted to package this new search tool as a plugin.  Very easy: Just create your view(s) and controller(s) then package them up in a zip.  Create a manifest, depending on version 2.33 of the web UI plugin and just make sure your scripts are unzipped to `SageTV\webserver\groovy\`

Details on creating SageTV plugins are beyond the scope of this document.

# What's Next? #

That's up to you.  I've provided the tools to create new web UI pages quickly and easily, now it's up to you to harness this ability and come up with something creative and/or productive.  Good luck and happy scripting!