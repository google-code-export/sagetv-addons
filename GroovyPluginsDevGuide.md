

# Introduction #

Recently, I [shared](http://forums.sagetv.com/forums/showthread.php?t=55166) my initial implementation of a SageTV plugin written entirely in Groovy script.  Basically, write your plugin in Groovy script, compile it to Java bytecode and then package it up and deploy it like any other Java plugin in SageTV.  After all, once you compile the Groovy script to bytecode it's all just Java.  The only special requirement is that your plugin must depend on the Groovy jar, which is already a plugin available in SageTV.

This was interesting and easy enough to do that I thought it'd be even more interesting to take this one step further.

Though writing plugins in Groovy script is convenient, it still suffers from one key drawback: a change in the source code requires a rebuild of the bytecode, which requires a restart of SageTV after upgrading.

Scripting is suppose to be dynamic so why not provide a platform where scripts don't need to be compiled into bytecode and are automatically interpreted on demand?  Well, now that platform exists!  I'm proud to present **GroovyPlugins: A dynamic scripting platform for SageTV plugins**.

## Pre-alpha, No Public Builds... Yet ##

I'm running a pre-alpha build of the platform on my test server right now with surprisingly high levels of success.  But it's still going to be a little while longer before I feel comfortable enough to put a beta build in the repository.  I'm looking for feedback from potential users on features, etc. that could be incorporated into such a platform.

# Platform Benefits #

  * Write plugins in Groovy script; say goodbye to Java and the overhead it requires when you want to write simple/small/quick plugins
  * Upgrade your plugin and have it dynamically use the new code immediately, usually without doing **anything**! (There are exceptions, see below.)
  * If setup properly, you can actually change your Groovy script _while your plugin is running_ and the new script changes will be picked up automatically.

# Why Groovy? #

Why not!? ;)  Seriously, for most tasks, a small Groovy script can do what would otherwise take hundreds of lines of Java code.  Groovy is, well, groovy!  It is a dynamic scripting language for the JVM and removes lots of boilerplate code for doing simple/common things.  Life is just easier and plugins get written faster with Groovy script!

# What Do I Need to Do? #

_This is coming..._

Notes to self: Bootstrap.groovy, PLUGIN\_ROOT\_DIR, Bootstrap must implement SageTVPlugin, basic example, basic example converted to handle live changes to .groovy files.

# What Restarts Can be Avoided and When? #

By writing your plugin in Groovy, you can avoid restarts of SageTV and even your plugin when you upgrade/modify the Groovy scripts in your plugin.  Here's the breakdown of the situations you may encounter:

## What Has to Be Restarted When? ##

If proper care is taken when writing your Groovy plugins, you can avoid having to restart anything on _most_ upgrade types.  If you properly bootstrap your plugin code then simply replace your Groovy script files and the changes should automatically be loaded and immediately deployed **with no user intervention**.

### No Restart Required When... ###

You properly bootstrap your plugin to allow for dynamic reloading of Groovy scripts (note to self: link to dynamic bootstrapping example) at runtime and you do **NOT** do any of the following:

  * Modify the Bootstrap Groovy script of your plugin; modifications to the Bootstrap class require a plugin restart.
  * Modify/upgrade any external jar files located on the SageTV classpath; such modifications will require a SageTV restart to take effect

### Your Plugin Must be Restarted When... ###

An upgrade of your plugin will require a restart of your plugin, but **NOT** a restart of the GroovyPlugins plugin nor a restart of SageTV under the following conditions:

  * Your upgrade modifies the Bootstrap class of the plugin **OR...**
  * You do not properly bootstrap your plugin to allow for dynamic code updating; if not bootstrapped for dynamic code reloading then any upgrade of your plugin code will require a restart of your plugin (but **NOT** a restart of the GroovyPlugins plugin nor a restart of SageTV)

### A SageTV Restart Required When... ###

An upgrade of your plugin will only require a SageTV restart if the upgrade involves modification of any jars on the SageTV classpath.  If you update any such jars then SageTV will need to be restarted.

# Warning: Misbehaving Scripts Will Wreak Havoc! #

For those familiar with SJQv4, you know it's also based on Groovy scripting.  However, in SJQv4, I cater to all users and do _some_ babysitting when it comes to execution of scripts.  Namely, I sandbox all user scripts in SJQv4 by running them in a separate JVM.  If a user gets into an infinite loop, they have a way out: just kill the JVM their script is running in.

After some serious thought, I am **not** doing any kind of "babysitting" for GroovyPlugins.  All Groovy scripts **will run in the same JVM as SageTV is running in.**  So if you write a misbehaving script it will wreak havoc on your SageTV server.  This is no different than a misbehaving Java plugin and for that reason I am not doing anything special for Groovy scripts.  So, **test your Groovy plugins before deploying them into production/plugin repository.**

# sagetv-addons License Required for More than X Plugins #

Without a sagetv-addons license, the GroovyPlugins platform will run up to _X_ (note to self: change this when I decide on the number) plugins simultaneously.  Once the limit is reached, other plugins will simply be skipped and won't start.