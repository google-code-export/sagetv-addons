

# Introduction #

One of the new key features of SJQ in version 4 is the removal of the server side rulesets and client side client configs.  The ruleset is completely removed in SJQv4 and is replaced by event based and schedule based task queuing.  In SJQv4, tasks are only ever added to the queue is response to an event or a schedule request.

Client configs are removed in SJQv4, but the need to script is not and it can't be removed while allowing the power and flexibility provided by SJQ.  However, the client config - and all its limitations - have been removed and replaced by a real scripting language: [Groovy](http://groovy.codehaus.org/).  In SJQv4, client tests are written and executed in Groovy.  The scripts are executed in a special environment known as the "SJQ Scripting Environment" (a.k.a. SJQ ScriptEnv).  The SJQ ScriptEnv simply provides access to the SageTV API within your scripts automatically along with some other variables of interest.

Though Groovy is the "official" scripting language, you are free to use other languages.  Details on how to do that are provided later in this document.

# Scripting Task Pretests #

If you wish to run a pretest for a task, it must be a script.  It must be a script supported by your task client.  All task clients include Groovy support and if you're running a Sun/Oracle JDK then it probably also includes JavaScript support.  When you start a task client it will report all the scripting engines available to you:

```
2010-10-18 12:18:42,563 INFO  [Agent]: The following scripting engines are available in this task client:
	Groovy Scripting Engine/2.0 [groovy]
	Mozilla Rhino/1.6 release 2 [js]
```

So this particular task client supports Groovy and JavaScript (Mozilla Rhino is Mozilla's JS implementation).  Groovy scripts are identified by the `.groovy` extension and JS script by the `.js` extension.  The file extensions supported for each scripting engine available is shown in the square brackets.  Scripts must match one of the shown file extensions in order to be executed by SJQ.

## The SJQ ScriptEnv ##

Scripts that run in the ScriptEnv have two key characteristics:

  1. "Global" variables providing access to the SageTV API are automatically initialized and made available to your script
  1. Scripts running in the ScriptEnv are automatically connected to the appropriate SageTV/SJQ server`**`

`**` The sagex-api plugin must be installed on your SageTV server that is running SJQ and the RMI service must be enabled in order for the API access to work from the ScriptEnv.

### Global Variables ###

When executing a script, the following global variables are available to your script:

`AiringAPI, AlbumAPI, CaptureDeviceAPI, CaptureDeviceInputAPI, ChannelAPI, Configuration, Database, FavoriteAPI, Global, LocatorAPI, MediaFileAPI, MediaPlayerAPI, PlaylistAPI, PluginAPI, SeriesInfoAPI, ShowAPI, SystemMessageAPI, TranscodeAPI, TVEditorialAPI, UserRecordAPI, Utility, WidgetAPI`

Each of these variables map the corresponding SageTV API as described here:

http://download.sage.tv/api/sage/api/package-summary.html

These objects provide **complete** access to the SageTV API from your scripts.

**NOTE:** This is very dangerous.  One can cause serious, irreversible damage to your SageTV server with a buggy script!  The simple rule is: if you aren't sure what your script is doing then **don't run it on your production server!**  Also, if you're using a script from someone else in the community then make sure you trust that person otherwise **don't run it against your production sever!**

"With great power comes great responsibility." (Spider-Man)

One simple statement couldn't be more true when talking about scripting in SJQ.  You can foobar your system with but a couple lines of script and there's no turning back so be careful!  With that said, it's this power that makes SJQ such a flexible tool.

### Additional Globals ###

`SJQ4_SCRIPT`: The complete, absolute path of the script being executed.

`SJQ4_ARGS`: A `String[]` of the command line args configured for the script

`SJQ4_METADATA`: A `Map<String, String>` of the environment variables configured for this task

`Tools`: An object containing a bunch of helper methods that provide additional functionality to your Groovy scripts.  See the [javadocs](http://sagetv-addons.googlecode.com/svn/trunk/sjq4-agent/doc/index.html) for details on the available methods and examples on how to use them.

### API Helpers ###

Starting with task client 1482, you can make use of various classes in your scripts that add additional functionality to the SageTV API.  Older task clients can simply manually install the `gtools.jar` file.  Download it [here](http://code.google.com/p/sagetv-addons/downloads/list).

Groovydocs (i.e. javadocs) describing the various classes available and what functionality they provide are available [here](http://sagetv-addons.googlecode.com/svn/trunk/SJQScripts/tools/docs/index.html).  These additional classes are Groovy scripts that provide helper methods like querying the age of media files, determining if something's recording, convenience methods for checking the size of media files, etc.  See the groovydocs for more details.

### Return Values of Pretests ###

A pretest is expected to return one of three values.  Each value denotes what the task client should do with the task next.  In Groovy, you return a value from the script by using a `return x` statement to end your script, where x is an integer.  If you don't explicitly return a value then return code 0 (i.e. PASS) is assumed.

  * 0: The pretest has passed and it is "safe" for this task to execute the defined exe
  * 1: The pretest has failed and the task should be returned back to the queue for future execution
  * 2: The pretest has determined that this task does not need to be run and will put the task in `SKIPPED` state.  `SKIPPED` tasks are equivalent to `COMPLETED` tasks except that only the pretest was run and not the actual exe.  `SKIPPED` tasks will never be assigned to another task client
  * >2: Same as returning 1

# Scripts as Task Executables #

In addition to writing scripts for pretests, you may also define a script as the actual executable for a defined task.  Simply prefix the script name with `script:` when defining your exe.

# Examples #

All examples are Groovy scripts and are complete, working examples.  You can copy the contents of each example, save it in a file with a `.groovy` extension and use it in a task definition.

## Push Task Back to Queue While Client is Connected ##

This example shows a pretest that checks if there is a client connected to the SageTV server.  If there is, the pretest returns 1, which means to return the task back to the queue for future assignment.

```
if(Global.GetConnectedClients().length > 0 || Global.GetUIContextNames().length > 0) {
   println("Returning task to queue because there is at least one client or extender connected to the SageTV server!");
   return 1;
} else {
   println("No clients or extenders connected to the SageTV server, returning 0!");
   return 0;
}
```

And that's it.  This pretest will continue to return the task to the queue as long as there is a client or extender connected to the server.  Once all clients disconnect then the task client will proceed to run the defined exe for the task.

The `println()` statements aren't necessary, but are nice to use because SJQ will capture all output from the pretest and log it so by using `println()` you can review why your tasks are being returned to the queue.

## Skip Task if EDL Already Exists ##

This example pretest checks that an edl doesn't already exist for the file about to comskipped.  If the edl already exists then there is no need to run comskip again so mark the task as `SKIPPED` and send it back to the server.

```
int segmentNumber = Integer.parseInt((SJQ4_METADATA.get("SJQ4_SEGMENTS"))) - 1;
String fileName = SJQ4_METADATA.get("SJQ4_SEGMENT_" + segmentNumber);
fileName = fileName.substring(0, fileName.lastIndexOf(".")) + ".edl";
println("File: " + fileName);
if(new File("/var/media/tv/" + fileName).exists()) {
   println("EDL already exists, marking task as skipped!");
   return 2;
} else {
   println("No EDL found, proceeding with comskip...");
   return 0;
}
```

The use of `SJQ4_METADATA` is significant here.  `SJQ4_METADATA` is a `Map<String, String>` of metadata for the task.  The contents of this map is totally configurable at the time the task is inserted into the queue.  However, there are some conventions to be followed and conventions are assumed in this code example.  Basically, any task inserted into the queue for a Sage object will provide well known values in the metadata map.  In this example, it assumed that a Sage `MediaFile` was attached to the task and the metadata map for a `MediaFile` is expected to contain certain data, of which `SJQ4_SEGMENTS` and `SJQ4_SEGMENT_n` is part of the data to be included.

# Yes, this looks scary, but... #

For those coming from SJQv3, this is different and probably a little more complicated, but in return you get an infinitely more powerful scripting environment.  And if you tell me, "hey, this is too much, I'm not a programmer!" then I'd counter with, "well, you are.  You used to be an SJQ language scripter, now you're going to become a Groovy scripter."  I say this because the old SJQ ruleset and client configs were a form of scripting.  The difference was that the SJQ "scripting language" was very small and very limiting.  The only difference now is that Groovy provides many more options and is a lot more flexible.  Yes, this flexibility means some learning has to be done, but this flexibility also means you will be able to do so much more!

Finally, I expect that over time you will be able to find a lot of Groovy scripts from the community in the user forums to perform most popular tasks and tests.  So for those who still think this is too much, well you should just be able to copy/paste your way to most of the popular tasks.

# Adding Other Scripting Languages (Windows Service/Console Task Clients Only) #

**NOTE:** Do not follow these instructions if you're using the plugin task client!  This is only valid for standalone installations of the task client.

So I promised that you didn't have to use Groovy exclusively for scripting and now I'll tell you how to add your language of choice.  First, some caveats:

  * I (Slugger) will only be supporting Groovy.  If you ask me for support on the forums, all of my solutions are going to be Groovy scripts.  I know most of the other scripting languages, but I feel like Groovy provides the best all around experience within the SJQ ScriptEnv so it's the one I've decided to support.  Other users may bring experiences and solutions in other languages and I may even assist in debugging other languages, but the "official" scripting language of SJQ is Groovy.
  * As said above, other languages will work, but Groovy provides the best over all experience/environment.  Though things typically worked in the other languages I experimented with (Sleep, Jython, JavaScript), they didn't quite work out of the box like Groovy did.  If you decide to use another language, you probably will run into problems.  If I'm able to help, I will, but I will **NOT** spend hours googling on your behalf to try to solve issues.  In other words, if you use another language, be prepared to do some research on how to access things like the engine bindings provided by SJQ.
  * Don't use Sleep unless you really know it inside out.  Why?  Sleep ignores parts of the Java JSR-223 spec making it impossible for SJQ to capture and log the output of Sleep scripts.  So if you use Sleep there will be no output logged on the server (though it is dumped to the stdout of the task client JVM).

With all of that said, here's how you add additional scripting engines to the task client.

  * Go here: https://scripting.dev.java.net/
  * Grab the **JSR-223 API implementation jar** for your language of choice
    * This may be a separate jar or it might be included in the language's main distribution; either way you need to ensure it is the JSR-223 implementation
  * Put the jar in the `engines/` directory of your task client installation
  * Restart your task client.  You should see a message in the initial log output showing support for the new language you've enabled