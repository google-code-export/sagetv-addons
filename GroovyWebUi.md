# Introduction #

v2.33 of the web UI added Groovy processing.  This allows anyone to add new functionality to the web UI via Groovy scripting.  The following documents outline, by example, how to achieve this.

# The Basics #

First, it's important to realize that the Groovy processing now built into the web UI will process **any** Groovy script it's pointed at.  With that in mind, I'll start with the basic "Hello, World!" examples.  Once you understand where scripts are read from, etc. you'll want to move on to the more complete example showing a full [MVC](http://en.wikipedia.org/wiki/Model%E2%80%93view%E2%80%93controller) approach to adding new web pages to the UI.

# Script Location #

Scripts are read from two places:

  1. The root directory of the war file.  You can modify/add groovy pages directly to the unpacked war at run time, but be aware that a Sage or web UI restart will destroy your changes.  For this reason, users wishing to add pages to the web UI **should not** add pages to this area.
  1. In `\SageTV\webserver\groovy\`  This is where users should be adding new web pages.  This directory is not wiped on a Sage restart.  You can use subdirectories to organize pages.  GSP and groovlets are both read and processed from this location.

**NOTE:** If you create a page with the same name as one included in the war file then the war file version is always read and used.  You cannot override files from the war.

# Groovlets: Hello, World! #

So let's just get you comfortable with adding any old script to the web UI.

Create a new file in the `\SageTV\webserver\groovy\` directory called `hello_world.groovy`

Put the following in the file:

```
println "${new Date()}: Hello, World!"
```

Then in your web browser, visit:

http://192.168.0.1:8080/sage/hello_world.groovy

Replace the IP address and port number as appropriate for your Jetty server configuration.

When you visit that page, you should get something like the following:

Sat Jan 07 15:30:29 EST 2012: Hello, World!

And there you have it, you're first Groovlet web page.  Details on what exactly is going on in a groovlet are available [here](http://groovy.codehaus.org/Groovlets).  Feel free to experiment.

## Sage API Access ##

Most of the time, you'll want access to the Sage API when adding pages.  That's easy enough because all Groovy scripts have access to everything on the SageTV classpath, including the sagex-api wrapper.  So let's add some API calls to the hello world example.  Modify the `hello_world.groovy` script so it reads as follows:

```
import sagex.api.*

def sageOS = Global.GetOS()
def mediaCount = MediaFileAPI.GetMediaFiles().size()

out << "<h1>SageTV on $sageOS</h1>\n"
out << "<p>Welcome to SageTV!  Your server has $mediaCount media files available!</p>\n"
```

Save the changes to the file then go and reload the web page in your browser.  You should now see something like the following in response:

<h1>SageTV on Windows XP</h1>
<p>Welcome to SageTV!  Your server has 100 media files available!</p>

There you have it... now it's just a matter of doing whatever you want to do. :)

# GSP: Hello, World #

GSPs are much like [JSPs](http://en.wikipedia.org/wiki/JavaServer_Pages).  The docs for GSPs are lacking, but [here's a starting point](http://groovy.codehaus.org/GSP).  Let's recreate the last example as a GSP.

In the same directory, create a file called `hello_world.gsp` and put the following in it:

```
<% import sagex.api.* %>
<h1>SageTV on ${Global.GetOS()}</h1>
<p>
   Welcome to SageTV!  Your server has ${MediaFileAPI.GetMediaFiles().size()} media file available!
</p>
```

Groovlets are more for pages with lots of processing and little output and GSPs are for pages with lots of markup and little processing.  That's not a hard and fast rule, but not a bad one to follow.  In reality, you'll want to marry the two concepts together using the [MVC](http://en.wikipedia.org/wiki/Model%E2%80%93view%E2%80%93controller) design pattern.  A full example of how to do this is available [here](GroovyWebUiMvc.md) and is the next tutorial you should advance to once you're comfortable with the concepts outlined in this document.