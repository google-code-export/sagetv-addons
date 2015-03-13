

# Introduction #

Welcome to SJQv4!  This is the latest evolution in customized task execution for your SageTV environment.  SJQv4 is a complete rewrite from the ground up and focuses on taking advantage of the latest features of SageTV 7.0.

## What is SJQ? ##

SJQ stands for Sage Job Queue and is a SageTV plugin that allows conditional execution of tasks for your SageTV system.  The most popular use of SJQ is to conditionally execute comskip on your SageTV recordings.  Conditional execution means that you can decide what to do on which objects based on the details of that object.  So, for example, you probably don't want to comskip recordings from HBO.  SJQ allows you to inspect the details of an object (i.e. a recording) and decide if you need/want to perform a task on it.  So in the case of comskip, you can inspect which channel the recording has come from and if it comes from one known not to have commercials, such as HBO, then you simply skip the execution of comskip on it.

### Parallel Task Execution ###

The SJQ task client is a multi-threaded application that will run multiple tasks in parallel.  You configure the amount of "resources" required to run a task on each task client and as long as there are enough free resources, a task client will continue to run more tasks.  You no longer need to run tasks serially.

### Distributed Task Execution ###

In addition to multi-threaded task clients, you can also have multiple task clients running on your LAN.  Imagine if you had 3 PCs on your LAN.  You could have three separate task clients all capable of running tasks simultaneously!

## Should I Use SJQv4? ##

Do you want to run tasks conditionally based on the state of an object or the state of your SageTV server?  If so, then SJQ is the tool for you!  Ever wanted to reboot your system on a weekly basis, but were afraid you might reboot during a recording?  With SJQ, you can be sure that it is "safe" to reboot before rebooting.

## Target Audience ##

There is a learning curve to SJQv4.  Some might even say it's rather steep.  I've worked hard to reduce the steepness of the curve compared to SJQv3 and I think I've done about the best I could while maintaining the maximum amount of power and flexibility this tool provides.  With that said, I definitely acknowledge that there is still a learning curve involved with SJQ.  But I also believe with enough time and effort, one can get setup with SJQ in but a few hours of effort.

A programming background would be beneficial, but it's not necessary.  I think programmers will find this tool much easier to use with that experience, but I firmly believe people without such experience can take full advantage of the SJQ system.  There is plenty of support to be found in the SageTV User Forums.  Even a complete novice can probably find the scripts they need within the user community and may not ever have to script anything.

Ultimately, if you feel this tool can benefit you and you're willing to put in the effort to learn it, anyone can harness the power of SJQv4!

## What's New? ##

SJQv4 is a complete rewrite from the ground up.  There are two key highlights in SJQv4:

  1. SJQv4 embraces and takes full advantage of the new SageTV 7.0 event model
    * Tasks are queued via events only; no more server side ruleset
  1. All conditional execution decisions are made client side in the "SJQ Script Environment"
    * No more conditional task insertion for some things and conditional assignment for other things; in SJQv4 you simply add tasks to the queue and let your task clients decide if/when that task should be executed

In addition, SJQv4 provides the SJQ Crontab, which allows the scheduling of arbitrary tasks on a regular interval.  No more wild and crazy ruleset hacks required to schedule a weekly restart of your SageTV server!

Rulesets and client configs are a thing of the past.  SJQv4 introduces the SJQ Scripting Environment for client side conditional task execution.  Now you write [Groovy](http://groovy.codehaus.org) scripts to decide if/when a task should be executed.  This is a major win for SJQ!  By moving to a proper scripting language, SJQv4 provides the ultimate environment for scripting all of your SJQ task needs.

# Getting Started #

## Installing the SJQ Server ##

SJQv4 server installation is easier than ever thanks to the new SageTV 7.0 Plugin Manager system!  To install the server, simply find the plugin in the list of available plugins and choose install.  Upgrades are just as easily handled.

### System Requirements for Server ###

The system requirements for SJQv4 are automatically handled by the SageTV Plugin Manager.  The plugin manager will not allow an installation of the plugin on a system that does not meet the minimum requirements.  Those requirements for an SJQv4 server installation are:

  * Java 1.5.0 or newer
  * SageTV Server 7.0.18 or newer

The SJQv4 server can only be installed on a SageTV server and not on PC clients.  This is because of how the event model in SageTV 7.0 works.

### Installing SJQ Server Plugin ###

To install from the default SageTV 7.0 STV, go to **_Setup > SageTV Plugins > All Available Plugins_** and find SJQ in the list of general plugins, click on it and choose install.  Upgrades are performed in the same fashion.

<img src='http://sagetv-addons.googlecode.com/svn/trunk/sjq4/media/images/wiki_install_plugin.jpg' />

### Installing SJQ UI Plugin ###

Thanks to the incredible work of tmiranda, SJQv4 finally integrates seamlessly into the SageTV STV.  In fact, there currently is no web UI for SJQv4 (though that may change in the future based on user feedback).  Though you can use SJQ without the UI mod by exclusively editing properties files and text files, you definitely want to install the UI mod.  The UI mod is currently the only way to visualize the task queues.

To install the UI plugin, simply go to **_Setup > SageTV Plugins > All Available Plugins_** and find the SJQ UI plugin in the list of UI Mods.  Click it and choose Install.  Upgrades are handled in a similar fashion.

<img src='http://sagetv-addons.googlecode.com/svn/trunk/sjq4/media/images/wiki_install_ui.jpg' />

### Installing sagetv-addons License ###

**NOTE:** Do you already have a license file for SageAlert?  If so, then you **DO NOT NEED TO DONATE AGAIN!**  The license file you use for SageAlert is also valid for SJQv4!  If you've donated twice simply send me an email and I'll return your second donation.  If it's been more than 60 days since your second donation, I will refund your donation less the PayPal fees (since I cannot recoup them after 60 days).

Obtain your sagetv-addons license file by making a [donation](https://www.paypal.com/cgi-bin/webscr?cmd=_s-xclick&hosted_button_id=AQV6A8JXY7XRJ).  Follow the instructions in the email you receive to install the license file in its required location.  Next, you need to register your email address associated with the license in SJQv4.  Do this by
going to **_Setup > Sage Job Queue (SJQ) > Configure_** and then entering your email address in the first box.  After entering your email address, you will need to restart SageTV.

After you restart, go back to the same menu and ensure the second box says that your engine is now licensed.

<img src='http://sagetv-addons.googlecode.com/svn/trunk/sjq4/media/images/wiki_register_lic.jpg' />

#### What Am I Missing Without a License? ####

The following limitations are imposed in SJQv4 without a sagetv-addons license file:

  * You can only register one task client with the server
  * The lone registered task client may only define a single task to execute

Details on why there is a license requirement in SJQv4 are available [here](DonationsFaq.md).  That document also contains details on how to create your own builds of SJQv4 that completely bypass the need for a license and unlock all features.  Though you can build your own versions of SJQ that remove the licensing requirement, you miss out on the following features:

  * Automatic and easy to install upgrades via the SageTV Plugin Manager
  * Builds are created and tested before they're released into the plugin repository
  * You don't have to go manually building your own packages and maintaining them

## Installing a Task Client ##

You need to install a task client on each physical host that you wish to have run tasks from the task queue.

### System Requirements for Task Client ###

To run the SJQv4 task client, you must have the following minimum system requirements:

  * Java **1.6.0** or newer
    * **NOTE:** Unlike the server, which only requires Java 1.5.0, the task client requires a JRE of at least 1.6.0.

The task client has been tested on Windows XP Home SP3 (32 bit), Windows 7 Home Premium (32 bit) and Linux 2.6 32 bit (Ubuntu).  The console client should work on any system with a JRE 1.6.0 or newer.

### Task Client Versions ###

There are three flavours of the task client in SJQv4:

  * **SageTV Plugin Task Client**
    * Install this version as a SageTV plugin.  Updates are automatically handled via the SageTV plugin manager.  This version will work on all supported platforms of SageTV.
    * Pros
      * Easy to install via plugin manager
      * Upgrades are easily managed via plugin manager
      * No separate application installation required
    * Cons
      * Requires SageTV server or client to run
      * Task client only runs while the SageTV instance you installed it in is running
      * Can only install one instance of task client as a plugin
  * **Windows Service Task Client**
    * Install this version as a standalone Windows service.  Installation and upgrades must be done manually as new versions of the task client are released.
    * Pros
      * Does not require SageTV; it is a standalone application
      * Can install and run SJQ tasks on machines that do not have SageTV installed on them
      * Can install more than one task client instance on a single physical host
      * Installs as a Windows service meaning automatic startup when Windows is started
    * Cons
      * Not as easy to install as plugin version
      * Installation/upgrading is a manual process
      * Only runs on Windows
  * **Console Task Client**
    * Install this version as a standalone application.  Installation and upgrades must be done manually as new versions of the task client are released.  This version is for non-Windows platforms or for Windows users who are not able to install Windows services on their host.
    * Pros
      * Multi-platform task client; tested on Windows XP, Windows 7 and Linux.  Should work on any platform with a JRE 1.6 runtime environment.
      * Does not require SageTV; it is a standalone application
      * Can install more than one task client on the same physical host
    * Cons
      * Not as easy to install as plugin version
      * Installation/upgrading is a manual process
      * Does not run in background (without some advanced setup/knowledge)

The Windows service version of the task client was tested on Windows XP Home SP3 (32 bit) and Windows 7 Home Premium (32 bit).  The wrapper used to enable the service is only available for 32 bit Windows (the 64 bit version, though available, is not free and has a rather expensive licensing fee).  Users of 64 bit Windows platforms may have success with the 32 bit service version, otherwise you will have to use the console version only.

More details on the service wrapper used can be found at [this web site](http://wrapper.tanukisoftware.com/).

Most users are encouraged to use the plugin version of the task client.  The Windows service and console versions of the plugin are considered advanced features and are for the more advanced SJQ user.

### Install Plugin Task Client ###

Installation of the plugin task client couldn't be easier!  From the SageTV STV just go to **_Setup > SageTV Plugins > All Available Plugins_**, go to the General tab and select the Sage Job Queue Task Client plugin.  Install it and that's it.  The plugin version of the task client will automatically register itself with the SJQ server.

<img src='http://sagetv-addons.googlecode.com/svn/trunk/sjq4/media/images/wiki_install_agent_plugin.jpg' />

Upgrades are handled in a similar fashion.

### Download Task Client ###

Download the latest version of the `sjq-agent` zip file from the [project download section](http://code.google.com/p/sagetv-addons/downloads/list).

If you are installing on any OS other than Windows then you must download the console version of the agent (`sjq-agent-4.x.x.x.zip`).  If you plan to install as a Windows service then be sure to grab the Windows service version of the agent (`sjq-agent-winsvc-4.x.x.x.zip`).

### Install as Console Application ###

These instructions apply to all OS versions.

First, unzip your download into any directory.  This will become the base installation directory for your task client.

Go into the newly created `sjqagent` directory.  This is the base installation of your task client install.  From here on out, this directory will be referred to as `$BASE`.

You need to make your `sjqagent.properties` file.  Go into the `$BASE/conf` directory.  Make a copy of the `sjqagent.properties.ref` file and save it as `sjqagent.properties`.

See the section on configuring your `sjqagent.properties` file for further instructions.  You should edit this file completely before starting the task client for the first time.

**NOTE:** You may also choose to wait and configure your task client from the SJQ UI.  The only option that **CANNOT** be modified from the UI is the port number that the task client listens on.  This must be changed via the properties file and requires a restart of the task client to take effect.  All other options can be modified from the UI.

### Install as Windows Service ###

First, unzip your download into any directory.  This will become the base installation directory for your task client.

Go into the newly created `sjqagent` directory.  This is the base installation of your task client install.  From here on out, this directory will be referred to as `%BASE%`.

You need to make your `sjqagent.properties` file.  Go into the `%BASE%\conf` directory.  Make a copy of the `sjqagent.properties.ref` file and save it as `sjqagent.properties`.

See the section on configuring your `sjqagent.properties` file for further instructions.  You should edit this file completely before starting the task client for the first time.

**NOTE:** You may also choose to wait and configure your task client from the SJQ UI.  The only option that **CANNOT** be modified from the UI is the port number that the task client listens on.  This must be changed via the properties file and requires a restart of the task client to take effect.  All other options can be modified from the UI.

Next, go to the `%BASE%\bin` directory.  Run the `InstallSJQ4Agent-NT.bat` file.  **NOTE:** You may need to gain Administrator rights to perform this task (especially on Vista or Windows 7).  This batch file will register the service with Windows.  You should see output similar to this:

<img src='http://sagetv-addons.googlecode.com/svn/trunk/sjq4/media/images/wiki_install_winsvc.jpg' />

As long as no errors are reported then you've successfully installed the task client as a Windows service.

### Configuring sjqagent.properties ###

**NOTE:** You may also choose to wait and configure your task client from the SJQ UI.  The only option that **CANNOT** be modified from the UI is the port number that the task client listens on.  This must be changed via the properties file and requires a restart of the task client to take effect.  All other options can be modified from the UI.

Before starting your task client for the first time, you need to configure it.  The `sjqagent.properties` file you created earlier contains very detailed comments and explanations of all valid values for this file.  Please read those comments carefully.

The most important thing - actually the only thing - you need to configure yourself is your task definitions.  If you do not configure task definitions then the task client will do nothing.  Here's a typical task definition that you can add to the bottom of the properties file:

```
task.mytask.exe=C:/bin/mytask.exe
# task.mytask.args=/f /g /t
task.mytask.test=C:/scripts/mytask_precheck.groovy
# task.mytask.testargs=
# task.mytask.schedule=* * * * *
# task.mytask.resources=100
# task.mytask.maxprocs=1
# task.mytask.rcmin=0
# task.mytask.rcmax=0
```

Adding the above block to your properties file will define a new task for this task client called 'MYTASK'.  As you can see, most of the lines are commented out and you don't need to uncomment them because those options include appropriate, valid default values.  The **only** value that **must** be defined for a task definition is the `exe` option, which defines the exe to be executed for the task.  Every other option, including the `test` option, is optional and will provide a default value.  The `sjqagent.properties` file you created during installation contains all the details about the meaning of each of these options so you are encouraged to head over to that file for more details.  Here is a quick run down:

  * `exe`: The path of the exe to execute when assigned this task; relative paths are relative to `$BASE/bin/`
  * `args`: Optional command line arguments to pass to the exe
  * `test`: An optional pretest script to execute prior to running the exe; see the [SJQ Scripting](Sjq4ScriptingSupport.md) wiki doc for more details on how these pretest scripts work
  * `testargs`: Optional command line arguments to pass to the pretest script
  * `schedule`: The ENABLED schedule for this task; see the properties files for more details
  * `resources`: The number of resources required to execute this task
  * `maxprocs`: The maximum number of simultaneous runs of this task permitted
  * `rcmin`: The minimum acceptable return code from the exe
  * `rcmax`: The maximum acceptable return code from the exe

#### NEW: Mapping server directories to client directories (MAPDIR) ####

Groovy scripts can use the `Tools.mapDir()` function to map server file paths to client file paths.  When running a task client on a host other than that of the SageTV server then you will need to use this function if the file paths reported by the Sage API are not UNC/network paths.

First, on each task client you configure the `agent.mapdir` setting in the `sjqagent.properties` file by adding a line like so:

```
# A mapping is separated by a comma and multiple mappings are separated by semicolons
agent.mapdir = C:\\tv\\,\\\\server\\tv\\;C:\\music\\,\\\\server\\music\\
```

**NOTE:** A future version of the SJQ UI may support editing this agent option from the UI.  Currently, you have to edit this option directly in the `sjqagent.properties` file.

Then in your Groovy scripts, anytime you receive a file path from a Sage API call, just filter it through `Tools.mapDir()` before using it:

```
for(Object mf : MediaFileAPI.GetMediaFiles("T")) {
   File[] files = Tools.mapDir(MediaFileAPI.GetSegmentFiles(mf))
   println files
}
```

#### NEW: Generate System Message on Task Failure ####

Starting with engine version 4.0.0.1392, you can configure your tasks to generate a SageTV system message when they fail.  Simply add the `sysmsgonfail` property to your tasks:

```
task.mytask.sysmsgonfail=true
```

Any task with this setting will generate a warning level system message in SageTV when the task's exe fails.  Nothing is generated when the test script fails.

**NOTE:** A future version of the SJQ UI may include the ability to modify this setting.  Until then, you must directly edit the `sjqagent.properties` file to set this option.

### Starting the Task Client ###

#### Plugin Version ####

The plugin version of the task client automatically starts and stops when the SageTV instance you installed it in starts and stops.  You may also temporarily disable the task client by configuring the plugin and disabling it.

#### Console Version ####

Go into `$BASE/bin/` directory and look for an appropriate startup script for your platform (`sjqagent.bat` for Windows, `sjqagent.sh` for Linux, etc.).  If there is one for your OS then run it, it should start the task client.  If not then you will have to start the JVM process manually.  From the `$BASE/bin` directory, run the following:

```
$ java -cp "../lib/commons-exec-1.0.1.jar:commons-io-1.4.jar:commons-lang-2.4.jar:log4j-1.2.15.jar:sagex-api.jar:sjq-agent.jar:sjq-common.jar:sjq-listener.jar" com.google.code.sagetvaddons.sjq.agent.Agent
```

#### Windows Service Version ####

The Windows service version will automatically start each time your system is started up.  However, you need to manually start it after initial installation.

<img src='http://sagetv-addons.googlecode.com/svn/trunk/sjq4/media/images/wiki_start_winsvc.jpg' />

**NOTE:** You could also start the service via the Control Panel.

### Register Task Client with SJQ Server ###

Once you install a task client and start it up, you must register it with the SJQ server.  Every task client you plan to run tasks on must first be registered with the SJQ server.

**NOTE:** The plugin version of the task client automatically registers itself with the SJQ server when it's installed.  The Windows service and console versions must be registered manually.

Go to **_Setup > Sage Job Queue (SJQ) > Register Client_** and enter the IP address or hostname of the task client you wish to register.  If you changed the default port of the task client during installation then you must tell SJQ which port the task client is listening on otherwise you only need to specify the IP/hostname.

<img src='http://sagetv-addons.googlecode.com/svn/trunk/sjq4/media/images/wiki_register_clnt.jpg' />

#### Warning About Using Hostnames Instead of IP Addresses for Task Client Registration ####
Though SJQ does support the registration of task clients by hostname, it is **highly recommended** that you exclusively use IP addresses **ONLY** when registering task clients.  If DNS is not properly configured on your LAN and/or hostnames can resolve to more than one IP address then you will run into many problems with SJQ.  SJQ expects that a hostname will resolve to only one IP address and that a hostname will resolve to the **exact same IP address on each and every host on your LAN.**  If this is not the case then simply register task client by IP address only.

## Upgrading Standalone Task Clients ##

If you're upgrading the standalone task clients by simply overwriting your current installation with the latest zip file then you **MUST** inspect the `engines` and `lib` directories after upgrading and removing any duplicate jars.  For example, v1482 of the task client upgraded groovy from 1.7.5 to 1.7.10.  So you **MUST** go into the `engines` folder after upgrading and remove the 1.7.5 groovy jar file.  Failure to do so will result in runtime errors.  The same must be done for the `lib` folder.

## What's Next? ##

By this stage, you now have the SJQv4 server running and you have at least one task client installed and running.  The next step is to add tasks to the queue for execution and then ensure at least one task client is configured to perform the tasks you insert into the queue.

Read the next section, "Adding Tasks to the Queue."  It will explain the various methods available for getting your tasks into the queue.  Once you have tasks in the queue, you'll probably want to go over the "Executing Tasks: A Tutorial" section as it will walk you through a step by step procedure for getting tasks into the queue and then configuring a task client to execute them.

# Adding Tasks to the Queue #

Please see tmiranda's [SJQv4 UI wiki](http://code.google.com/p/tmiranda/wiki/SJQv4UserInterface) for details on how to use the UI for SJQv4.  It includes details on how to queue up tasks for execution.

# Executing Tasks: A Tutorial #

## Tutorial One: Configuring Comskip ##

See the [first comskip configuration tutorial](Sjq4Comskip1.md) wiki doc for details on how to configure comskip under SJQv4.