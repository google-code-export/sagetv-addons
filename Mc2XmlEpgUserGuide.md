

# Introduction #

The mc2xmlepg plugin for SageTV 7 provides an alternative to the Sage EPG data service.  There are some advantages and disadvantages to this plugin, all of which will be discussed throughout this user guide.

Eventually, the Sage EPG data service will no longer be available and when that happens this is one of your alternatives for getting listings data into your SageTV EPG.

Though this is a native SageTV plugin, there are some special installation and uninstallation instructions that **must be followed** so please read those sections carefully.

# Features #

## New features and enhancements vs. the Sage EPG service ##

  * Continued access to EPG data after SageTV discontinues its EPG data service
  * Support for multiple lineups`**`
  * Advanced filtering and preprocessing of EPG data prior to it being added to the EPG through the use of Groovy scripting`**`
  * Ability to pull in additional EPG data from other sources to supplement or correct/update the available EPG data through the use of Groovy scripting`**`
  * Ability to trigger on demand updates of the EPG data through the SageTV STV`**`

`**` Feature available in licensed version only; unlicensed version provides support for a single lineup and no advanced features

## Incomplete or unstable Sage EPG features ##

  * Team roles are applied to shows in some instances where it may not necessarily make sense

## Missing Sage EPG features: Not available ##

  * <strike>Season & episode numbers are not available</strike> **(Added to licensed version in build 1713)**
  * Some airing details (DD5.1, others) not available; some are available (HDTV, stereo, CC)
  * Season/Series premiere **now available in licensed version (because it requires use of thetvdb.com API)** and season/series finale details still not available
  * Some types of credits aren't available (Writer, Producer, etc.); Actor, Host, Director, Guest Star seem to be the only ones available
  * An actor's character details are not available (i.e. Michael C. Hall as Dexter Morgan is not available, but, of course, Michael C. Hall is listed as an actor in the show).  These details will remain available for those series that are already in your wiz.bin prior to switching EPG sources; new shows starting after you switch data sources will not have this data available.
  * Channel logos not available. (NOTE: There are other plugins available that provide channel logos)

## Known Issues ##

  * The Zap2It show ID received from mc2xml may have zeroes prefixed to the ID number, which will confuse Sage into thinking it hasn't recorded some episodes even though it has.  For example, using the Sage EPG service, the show ID for "Family Guy" is `EP296001nnnn`, but using mc2xml, the show ID is `EP00296001nnnn`.  Notice that mx2xml is prefixing the leading zeros after the `EP`?  I have to investigate if it's safe to simply remove the prefixed zeros always or if I should just leave it.  For now, I'm just leaving it.  This will require users to manually remark airings as watched to prevent duplicate recordings of such episodes.

# System Requirements #

To use this plugin, you need:

  * SageTV v7.1.9 server (nothing older nor nothing newer will work)
  * Windows and Linux servers supported
  * Java 1.5 or Java 6 (Java 7 untested, unsupported)
  * The latest version of [mc2xml](http://mc2xml.dyndns.org)

All of these prerequisites are enforced by the SageTV plugin manager except the mc2xml requirement.  Details on how to setup mc2xml are outlined below.

# Plugin Installation #

## Download mc2xml ##

Download mc2xml for your OS by visiting the [app's web site](http://mc2xml.dyndns.org).  It is recommended that you copy the exe into a directory located on your system path.  For Windows users, that would be a location such as `C:\Windows\System32\` and for Linux users it would be a location such as `/usr/local/bin/`.  You will probably need elevated permissions to copy the exe to such locations, please refer to your OS documentation for details on how to achieve the required access to the file system to perform these actions.

Copying the exe to the system path is not necessary, but if you don't put it on your system path then you must remember where it is located so that you can point the plugin to it later on in the installation.

## Verify mc2xml Installation ##

If you copied it to your system path you can ensure that it's working properly by opening a command window and simply typing `mc2xml` at the prompt.  If it's on your system path then you should get output similar to the following:

### Windows ###

<img src='http://sagetv-addons.googlecode.com/svn/trunk/mc2xml-epg/media/wiki/mc2xmlepg_001.jpg' />

At this point, simply ignore and close the dialog window that pops up.  Details on how to configure that popup will be discussed later.  If you don't get output as shown, then you probably didn't copy the exe to a location on the system path.

### Linux ###

```
$ mc2xml
Loading ..... : mc2xml (c) <mc2xml@gmail.com> (v1.1)
Reminder .... : Unauthorized redistribution prohibited.
Reminder .... : If this software is useful, please donate!
Missing -g
```

Again, ignore the output, just ensure you're getting similar output, which means that the program is being found in the system path.  Details on how to configure mc2xml will be provided later.

## Install SageTV plugin ##

Go to the list of available SageTV plugins in your Sage STV and choose to install the mc2xml EPG plugin.

<img src='http://sagetv-addons.googlecode.com/svn/trunk/mc2xml-epg/media/wiki/mc2xmlepg_002.jpg' />

**IMPORTANT:** If Sage says you must restart SageTV **DO NOT** perform the restart at this time.  Click "Restart Later" and continue on with the steps below.  Restarting now is not catastrophic, but it just means you'll have to do a second restart later on.

## Remove video sources ##

Now it's time to cut ties with the SageTV EPG service.  You **must** reconfigure all your video sources within SageTV, there is no choice.

From the standard STV, go to **Setup > Setup Video Sources**, which will bring you to the following screen:

<img src='http://sagetv-addons.googlecode.com/svn/trunk/mc2xml-epg/media/wiki/mc2xmlepg_003.jpg' />

Click on each video source you have configured and on the next screen for each, click "Remove Source" on the left hand side.  Once you have removed all video sources then continue on to the next step.

## Configure plugin ##

It's now time to configure the EPG plugin.  Using the standard STV, select **Setup > SageTV Plugins > Installed Plugins**

Scroll down and click on the **mc2xml EPG Plugin** item then click on **Configure Plugin**.  **NOTE:** If the Configure Plugin option is not available then you must restart SageTV at this point then come back and load the plugin configuration screen.  In this case, you won't be able to avoid the multiple restarts.

<img src='http://sagetv-addons.googlecode.com/svn/trunk/mc2xml-epg/media/wiki/mc2xmlepg_004.jpg' />

**Location of mc2xml:** If you installed mc2xml into the system path earlier then you can just leave this option untouched.  If you didn't then you must click the button on the right and point it to the location of the mc2xml executable.

The remaining options can be left untouched for now.  Their meanings are discussed later in the guide.

## Configure mc2xml ##

Now you must configure mc2xml for each lineup you wish to download and make available to SageTV.

### Multiple Lineup Restrictions ###

The ability to download more than one lineup is only available in the licensed version of the plugin.  If you're using the unlicensed version you can still configure multiple lineups, but SageTV will only be able to see the lineup configured with the **lowest** lineup id.

You can, however, assign the same lineup to more than one input source.  So if you have more than one tuner they can all share the same lineup (or copy of a lineup).  You just can't assign different, unique lineups to different tuners with the unlicensed version of the plugin.

### Windows ###

Open a command window and go to the following (equivalent) location:

`C:\Program Files\SageTV\SageTV\plugins\mc2xmlepg\`

**NOTE:** You may need administrator access to perform these tasks depending on the version of Windows you're using.

Create a directory, name it whatever you like (I suggest a short form of your lineup name, but anything will do).

Let's say you named it `sd_east`

Go inside the new directory and create a file called `lineup.properties` and put the following contents inside the file:

```
id=1000
name=Shaw Direct East (Classic)
```

The `id` **must** be a positive integer and **must** be unique across all lineups you create.  The `name` value can be anything and is how the lineup will be presented to you within SageTV.

Remember, if you're using the unlicensed version and you create more than one lineup, SageTV will only be able to see the lineup with the **lowest** lineup id value.  Keep that in mind as you configure your lineups.

Now, perform the following command **inside** this new lineup directory you just created.

```
mc2xml
```

**NOTE:** If `mc2xml` is not on your system path then you must provide the full path of the command.

You should get output similar to the following:

<img src='http://sagetv-addons.googlecode.com/svn/trunk/mc2xml-epg/media/wiki/mc2xmlepg_001.jpg' />

In the popup window, ensure the options are selected as follows:

  * Service: At this time, only the **microsoft legacy** service is supported by this plugin, so ensure that option is selected
  * The following options **MUST** be selected: `-U (UTF8), -F`  Failure to check these options will result in failed EPG updates.
  * The following options **MUST NOT** be selected: `-u (UTC), -a, -A`  Failure to unselect these options will result in unpredictable EPG data and/or failed EPG updates.

Finally, enter your zip/postal code and country code (`ca` for Canada or `us` for USA, etc.).  Then click `Ok`.

You will then get a dialog asking you to select a channel lineup:

<img src='http://sagetv-addons.googlecode.com/svn/trunk/mc2xml-epg/media/wiki/mc2xmlepg_005.jpg' />

Click on your lineup of choice then click "Ok".  Your lineup data should be downloaded into a file called `xmltv.xml`

<img src='http://sagetv-addons.googlecode.com/svn/trunk/mc2xml-epg/media/wiki/mc2xmlepg_006.jpg' />

You can confirm the existence of the xml file.  Assuming it was created, you have successfully configured your lineup.  Repeat these steps for each lineup you wish to create.

### Linux ###

TODO

### mc2xml Config Files ###

mc2xml allows you to only produce output for a subset of the channels in your lineup and to rename channels in the lineup.  This is done through the use of additional config files.  This plugin will honour those config files, if they're found in your lineup directories.  To have the  config files processed, simply create them as directed on the [mc2xml web site](http://mc2xml.dyndns.org).

## Restart SageTV ##

Restart your SageTV server.  Upon restart, you should be taken the video source configuration wizard.

## Reconfigure video sources ##

Reconfigure each of your video sources.  Now when you select your EPG lineups, you should be presented with the lineups you configured earlier.

**NOTE:** Be patient when configuring lineups as a complete EPG download and update must be performed on each configured lineup before your channels are presented.  Depending on the number of channels, your internet speed, and your hardware, this can be _very_ slow - up to 20 minutes.  Be patient and if after 20 minutes the screen is stuck here:

<img src='http://sagetv-addons.googlecode.com/svn/trunk/mc2xml-epg/media/wiki/mc2xmlepg_007.jpg' />

Then check the `sagetv_0.txt` log file for errors or the `\SageTV\plugins\mc2xmlepg\logs\*.log` files for errors.

Once the EPG update is complete, you can select your channels, etc.  From this point forward, you complete your lineup configurations just like using the SageTV EPG service.

# Upgrading the Plugin #

Upgrading the plugin is rather straightforward, simply apply the plugin updates when you're alerted about them.  You do **not** need to reconfigure mc2xml after plugin updates.

Please apply updates when they're made available, especially during the v0.x betas.  Unlike most plugins, if you're using this one then it's providing a rather critical service to your SageTV system and running the latest version available will be critical to proper EPG data being inserted into your wiz.bin.

# Uninstalling Plugin #

## Before Uninstalling ##

If you decide to stop using the mc2xml plugin please be aware that when you switch back to the Sage EPG service that you might end up recording shows that you've already recorded.  This is because some shows use a zero padded show ID with mc2xml and those leading zeros are stripped by the Sage EPG service.  Since the show IDs aren't identical, Sage will not consider those airings recorded when you switch EPG services.  This is more inconvenient than anything, but it is something to be aware of.

Follow the steps below to uninstall this plugin.

## Remove video sources ##

First you must remove all your video sources and reconfigure them after completely removing the EPG plugin.

Go to **Setup > Video Sources**

<img src='http://sagetv-addons.googlecode.com/svn/trunk/mc2xml-epg/media/wiki/mc2xmlepg_003.jpg' />

Remove each video source.

## Uninstall SageTV plugin ##

Go to **Setup > SageTV Plugins > Installed Plugins**

<img src='http://sagetv-addons.googlecode.com/svn/trunk/mc2xml-epg/media/wiki/mc2xmlepg_008.jpg' />

Select and uninstall the mc2xml EPG plugin.

**NOTE:** If Sage asks you to restart SageTV then say **NO** and do it later.  You need to modify the `Sage.properties` file before restarting so don't bother doing a restart right now.

## Stop SageTV ##

Stop SageTV at this point.  Before restarting it, do the next step.

## Remove EPG plugin property ##

Edit your `Sage.properties` file and look for a line that starts with `epg/epg_import_plugin`

The line should actually read:

```
epg/epg_import_plugin=com.google.code.sagetvaddons.mc2xmlepg.EPGImportPluginMc2Xml
```

Change it so it reads:

```
epg/epg_import_plugin=
```

Making this edit is what actually removes the EPG plugin from your system.

## Restart SageTV ##

Now restart SageTV.  Your server will now switch back to the SageTV EPG service.

## Reconfigure video sources with Sage/alternate EPG service ##

When you connect to the server for the first time you should be taken back to the video source configuration wizard, which will allow you to reconfigure your video sources.  At this stage, you just reconfigure your video sources as normal.

# Advanced Features #

## License Required ##

All of the advanced features discussed below are only available in the licensed version of the plugin.  Details on licensing are available [here](http://code.google.com/p/sagetv-addons/wiki/PluginPricing) and licenses can be purchased from [here](http://code.google.com/p/sagetv-addons/wiki/PluginLicenseStore).

## Multiple Lineup Support ##

To use more than one unique lineup with this plugin requires a license.  The unlicensed version allows you to configure multiple lineups, but only one lineup will be made visible to SageTV by the plugin if it's unlicensed.  The lineup with the lowest configured lineup ID is the one that will always be made visible to SageTV.

Be aware, however, that you can assign a single lineup to more than one tuner.  Sage also allows you to create copies of a lineup and assign that copy to separate tuners.  So users with multiple tuners that all share the same lineup will not be hindered by the unlicensed version of this plugin because you'll be able to assign your single lineup (or copies of it) to each tuner on your system.

## Show & Airing Filters ##

By using filters, you can massage the details of shows and airings before they're inserted into the Sage `wiz.bin` database.  This provides some very interesting use cases and can help correct EPG errors from the source.

### Filter Script Locations ###

All filters are [Groovy](http://groovy.codehaus.org) scripts and must be inserted into `\plugins\mc2xmlepg\airing_filters\` or `\plugins\mc2xmlepg\show_filters\`.

### Object Injection ###

The filters are provided two variables: `_src_` and it is an [Airing object](http://code.google.com/p/sagetv-addons/source/browse/trunk/mc2xml-epg/src/com/google/code/sagetvaddons/mc2xmlepg/data/Airing.groovy) if it's an airing filter or a [Show object](http://code.google.com/p/sagetv-addons/source/browse/trunk/mc2xml-epg/src/com/google/code/sagetvaddons/mc2xmlepg/data/Show.groovy) if it's a show filter.

The other object is called `_log_` and it is a logger object that allows you to log output from your script to a private file.  The file sits along side the location of the script.  So if your script is called `show_filters\filter1.groovy` then anything you log via this object will be written to `show_filters\filter1.log`.  The details of how to use the logger object can be gather from [here](http://code.google.com/p/sagetv-addons/source/browse/trunk/mc2xml-epg/src/com/google/code/sagetvaddons/mc2xmlepg/log/SimpleFileLogger.groovy).  **NOTE:** All logger files are cleared at the start of each EPG update.  This means the log files created always represent the output of the latest run only.

### Object Modification ###

Your filter script is free to modify the `_src_` object's properties, but you cannot reassign the object itself.  If you do reassign the object then your changes to the object will not be processed.  In other words, think of the `_src_` variable as a `final` variable, even though that can't be enforced.

```
// _src_ is an Airing
_src_.hdtv = true // This is fine and the hdtv flag will be set to true in wiz.bin for this airing
```

```
// _src_ is an Airing
_src_ = null // Ooops, you've lost the reference to the object, this filter script is now useless, the changes to _src_ are lost, and you're just wasting time
```

```
// _src_ is an Airing
_src_ = new Airing() // Ooops, you can't change the reference that _src_ is pointing to; again now anything you do to _src_ will be ignored
```

```
// _src_ is an Airing
_src_.hdtv = false
_src_ = new Airing() // Ooops, the hdtv change will be processed, but anything you do to _src_ after this line is not processed
```

So just remember not to modify the `_src_` reference itself, just modify the object's properties as desired.

### Execution Order ###

**Every** show in the xml input is passed to **every** filter in the `show_filters` directory before it is inserted into the `wiz.bin`.  Note, however, that in the case of shows, a show is only processed once per EPG update even though a Show appears multiple times in the xml input.  Filters are executed in alphabetical order and the `_src_` object received in a filter contains the modifications of the filters executed before it.  In other words, the filters are chained together to produce the final filtered result of an object.

The airing filters work exactly the same way except that since every airing in the xml input is unique, the filters are run on every single airing in the xml input.

### Script classpath ###

The filter scripts use the same classpath as SageTV so anything in the Sage classpath is in your script's classpath.  In addition, the directory that the script is running from is also in the classpath.  If you needed to add jars or .class files for a filter script, you can use the filter directory as a classpath root.

### Execution Time Considerations ###

Remember that these filter scripts are all executed during an EPG update.  Filters should be relatively fast and **well tested**.  A filter that throws an exception will not halt your EPG update, but will usually slow down the EPG update significantly.

You can do complicated/relatively slow things in your filters, but just be aware that doing so means your EPG updates will take that much longer.

### Example: Fix Fox HD flag ###

So for me, the HD flag of some Fox shows is not properly set.  When it's not set then Sage considers the airing to be SD and in this case Sage tends to choose other SD channels to record the shows from instead of preferring to record the HD airing.  Before this, I would simply restrict the favourite to the Fox HD channels, which is fine, but can produce conflicts unnecessarily.  In other words, I want to record the HD airing, but if I can't because of conflicts then I want it to be able to consider the SD airings of the show as well.

The fix is simple: Filter the Fox HD airings and ensure the HD flag is set to true for all airings on the channels.  With an airing filter, this is real easy:

```
import sagex.api.*

def cal = Calendar.getInstance()
cal.setTimeInMillis(_src_.start)
if(cal.get(Calendar.HOUR_OF_DAY) < 18 && cal.get(Calendar.HOUR_OF_DAY) > 2) return
def chan = ChannelAPI.GetChannelForStationID(_src_.stationId)
if(ChannelAPI.GetChannelNumber(chan) ==~ /287|291/) // Fox HD channels (for my lineup)
    _src_.hdtv = true
```

This filter ensures that every airing on my Fox channels between 6pm and 3am daily has the HD flag set to true.  I do this because one HD channel (287) is from the east and the other is from the west (291) and the prime time schedule on 291 is 11pm-2am for me.

So the filter simply examines the start time of the received Airing object.  If it falls in the given time frame then I inspect the channel of the airing and if it's a Fox channel then I set the hdtv property to true.  That's it.  Just like that, I've corrected the EPG errors I've been receiving for years.  Now the Sage scheduler can properly consider HD vs. SD airings of Fox shows.  Just drop the above script into the `airing_filers` directory and the filter will be applied on each and every EPG update.  Now the HD flag is properly set for my Fox HD channels.

## Channel Generators ##

**NOTE:** Channel generators were added in build 1638.

If your lineup(s) are missing channels, you can use a channel generator to add additional channels to your lineup.  Be aware that channels you add will not have any EPG data unless you create the EPG data with show and airing generators.

### Script Location ###

All channel generator scripts must be placed in the `SageTV\plugins\mc2xmlepg\channel_generators\` directory.

### Runtime Environment ###

All scripts must be Groovy scripts and **must** return a value of type **Collection<[Channel](http://code.google.com/p/sagetv-addons/source/browse/trunk/mc2xml-epg/src/com/google/code/sagetvaddons/mc2xmlepg/data/Channel.groovy)>**.

All channel generators receive a [logger object](http://code.google.com/p/sagetv-addons/source/browse/trunk/mc2xml-epg/src/com/google/code/sagetvaddons/mc2xmlepg/log/SimpleFileLogger.groovy) named `_log_` and another variable named `_chans_`.  The `_chans_` variable is a **List<[Channel](http://code.google.com/p/sagetv-addons/source/browse/trunk/mc2xml-epg/src/com/google/code/sagetvaddons/mc2xmlepg/data/Channel.groovy)>** that contains all of the channels created for the lineup currently being processed.

When a script returns a collection, all of those Channel objects will be added to the `wiz.bin` as valid channels.

### Station ID for Generated Channels ###

When generating new channels, you **must** ensure that the station id you generate for each channel **does not** conflict with any id that may be included with the channels from your downloaded lineups.  All ids must be **positive** integers.  I suggest starting at a number close to 2^32 since these numbers are less likely to be used by mc2xml channels.

### Example: Add Missing NFL Sunday Ticket HD Channels ###

So for my lineup, sometimes my provider adds additional HD channels for NFL on Sundays and these channels don't get added to my linup upstream in time.  Not a problem now, since I can just ensure they're always in my lineup.

```
import sagex.api.*
import com.google.code.sagetvaddons.mc2xmlepg.data.*

def chanId = 1000
def sageChans = ChannelAPI.GetAllChannels()
def chans = []
for(def name : ['NFLHD1', 'NFLHD2', 'NFLHD3', 'NFLHD4', 'NFLHD5']) {
    def exists = false
    for(def c : sageChans) {
        if(ChannelAPI.GetChannelName(c) == name) {
            exists = true
            break
        }
    }
    if(!exists)
        chans.add(new Channel(callsign: name, longName: "NFL Sunday Ticket HD ${name[name.length() - 1]}", network: 'Premium Sports', stationId: chanId))
    ++chanId
}
return chans
```

So this generator only generates the channel if it doesn't already exist.  Also notice that my channel IDs start at -1000.

## Lineup Editors ##

**NOTE:** Lineup editors were added in build 1639.

Lineup editors allow you to add additional channels to a mc2xml lineup.  Usually you'd do this where you created new channels with a channel generator and want to add those channels to one or more of your mc2xml lineups.

### Script Location ###

All lineup editor scripts must be placed in the lineup directory of the lineup you wish to add it to and it must be named `channels.groovy`.  Each lineup can have only one lineup editor script.

### Runtime Envrionment ###

The lineup editor is injected with a variable `_lineup_`, which is a **Map<Integer, String`[]`>** and is the mapping of channels to channel numbers of the lineup currently being processed.  You may or may not want to use this information in your editor script.

### Required Return Value ###

The `channels.groovy` script must return a value of type
**Map<Integer, String`[]`>**.  The key is the station id of the channel to be added to the lineup and the value is a string array of channel numbers that channel is located at on this lineup.

### Example: Add Missing NFL Sunday Ticket HD Channels ###

So this example completes the channel generator example above.  In that example, I added missing Sunday Ticket HD channels.  Now I'm going to map those channels to my lineup.

```
import sagex.api.*

def map = [(1000): ['436'], (1001): ['437'], (1002): ['438'], (1003): ['439'], (1004): ['440']]
def itr = map.keySet().iterator()
while(itr.hasNext()) {
    def id = itr.next()
    if(ChannelAPI.GetChannelForStationID(id) == null)
        itr.remove()
}
return map
```

So in this example, I'm mapping station id -1000 to channel 436, id -1001 to channel 437, etc.

Then I remove channels from the map that don't exist.  This would happen in the case where the NFLHDx channel was already in my providers lineup.  In this case, I don't want to use a self generated channel, so I wouldn't and need to remove the mapping here.

## Show Generators ##

**NOTE:** Show generators were introduced in build 1652.

You can use show generators to modify or add shows to your EPG.  All show generators are Groovy scripts.

To be inserted into the EPG, a script must return an object of type **Collection<[Show](http://code.google.com/p/sagetv-addons/source/browse/trunk/mc2xml-epg/src/com/google/code/sagetvaddons/mc2xmlepg/data/Show.groovy)>**.  When a script returns a collection, each entry of that collection is added to the EPG database.  Once shows are added, you must use airing generators to creating airings of the shows in the EPG.  Without airings, the shows you create with these generators will never show up in your EPG listings.

Generators are independent of each other and their execution order is not guaranteed.  Last one to insert wins, the others are lost and since the order of generator execution is unknown, you need to make sure that your generators aren't overwriting each other unexpectedly.

### Script Location ###

All show generators must be located in `SageTV\plugins\mc2xmlepg\show_generators\`.  Every `.groovy` file found in this directory will be executed.

### Runtime Environment ###

Generators use the same runtime environment as filters, so read up on that section for more details.  There is one difference: Generators do not have any variables injected into the script (except for the logger: `_log_`.  Remember, generators are to generate shows so you need to build the collection so there's no need to pass any variables to generator scripts.

### Warning: External IDs for Generated Shows ###

Be careful when generating your own show objects.  If you generate a show with an external ID that is the same as one received from mc2xml then you will overwrite that show object in the `wiz.bin`.  It is assumed that users who execute show generators know what they're doing and so no sanity checks, etc. are performed prior to inserting the results of the generators into `wiz.bin`.  If you start to see weird results in your EPG then you may want to disable your show generators.

### Example: Basic Show Collection Generator ###

I'm personally not using show generators in my environment yet, so I'll just provide a simple Groovy script that generates a new show object.  If you added this generator to your installation then this show object would be added into your `wiz.bin` on every EPG update.

```
import com.google.code.sagetvaddons.mc2xmlepg.data.Show

def show = new Show(externalID: 'MV0000000000', title: 'My Movie' description: 'This is a fake movie to be inserted into the Sage database.')
return [show]
```

Basic generator script... of note, again, be aware that the external id you generate should be unique from anything received from mc2xml.  If not, then you're going to end up overwriting the show objects received from mc2xml.

## Airing Generators ##

**NOTE:** Airing generators were introduced in build 1635.

You can use airing generators to modify or add airings to your EPG.  All airing generators are Groovy scripts.

To be inserted into the EPG, a script must return an object of type  **Collection<[Airing](http://code.google.com/p/sagetv-addons/source/browse/trunk/mc2xml-epg/src/com/google/code/sagetvaddons/mc2xmlepg/data/Airing.groovy)>**.  When a script does, the airings are then inserted into the Sage EPG.  Generator scripts are independent of each other and the order in which they are run is not guaranteed.  Furthermore, if two or more generators create an airing for the same channel at the same or overlapping times then the results are undefined.  In other words, the Sage EPG does not allow more than one airing on a channel at the same time and enforces this by removing older airings.  Last one to insert wins, the others are lost and since the order of generator execution is unknown, you need to make sure that your generators aren't overwriting each other.

### Script Location ###

All airing generator scripts must be located in `SageTV\plugins\mc2xmlepg\airing_generators\`.  Every `.groovy` file found in this directory will be executed.

### Runtime Environment ###

Generators use the same runtime environment as filters, so read up on that section for more details.  There is one difference: Generators do not have any variables injected into the script, except for the logger, `_log_`.  Remember, generators are to generate airings so you need to build the collection so there's no need to pass any variables to generator scripts.

### Example: Fix NFL Sunday Ticket Listings ###

For my lineup, the NFL Sunday Ticket SD channels are properly filled in with game data, but the HD channels aren't - they all say "No Data".  Because of this, if my Dolphins game is only available on NFLST on a given week then Sage will only find it in SD.  Until now, I've simply created manual recordings to grab the games off of the HD channels.  This is ok, but it requires I remember to set the manual recording then I also have to go and remember to create an override for SRE to monitor the game as well.  Instead, I simply modify the EPG to fill in the game data for the HD channels.  A slightly modified version of the script I'm using is provided below.  It's modified to aid in explaining a few things.

```
import sagex.api.*
import java.text.*
import com.google.code.sagetvaddons.mc2xmlepg.data.Airing

def input = """
<epgmods>
    <record start="201110161300" channel="NFLHD1">
        <title>NFL Football</title>
        <episode>Carolina Panthers at Atlanta Falcons</episode>
    </record>
</epgmods>
"""

int[] findStationId(String callsign) {
    def ids = []
    Database.FilterByMethod(Database.FilterByBoolMethod(ChannelAPI.GetAllChannels(), 'IsChannelViewable', true), 'GetChannelName', callsign, true).each {
        ids.add(ChannelAPI.GetStationID(it))
    }
    return ids
}

def dateFmt = new SimpleDateFormat('yyyyMMddHHmm')
def xml = new XmlSlurper().parseText(input)
def entries = []

recLoop:
for(def record : xml.record) {
    def start = dateFmt.parse(record.@start.text()).getTime()
    def airings = Database.FilterByMethod(Database.GetAiringsOnViewableChannelsAtTime(start, start + 1, true), 'GetShowTitle', record.title.text(), true)
    for(def airing : airings) {
        if(ShowAPI.GetShowEpisode(airing) == record.episode.text()) {
            def extId = ShowAPI.GetShowExternalID(airing)
            findStationId(record.@channel.text()).each {
                entries.add(new Airing(externalId: extId, start: start, end: start + (3L * 3600000L), hdtv: true, live: true, stationId: it))
            }
            break recLoop
        }
    }
}
return entries
```

So this generator script parses the XML at the top of the file and for each record in the XML, it creates an airing on the given channel.  This particular script will only create the airing if it finds the SD airing of the game.  This ensures that Sage doesn't try to record the game more than once.  So in this case, the script is adding the HD airing of the Panthers/Falcons game to channel "NFLHD1" on Oct 16 at 13:00.  I'd obviously have a lot of additional records in the XML for all the HD channels, but I stripped it down to one to make the example as small as possible.  So for this particular generator, I have to modify the XML each week to update the HD channel listings for football.  Not perfect, but not bad either.  A better version of this script might download the HD listings data from their provider's web site, parse it out, and then fill in the HD channel listings.  That would be much more automated an approach, but also makes the script much more complicated for a generator example for this user guide. :)

## Force EPG Update via STV ##

If you're running the licensed version of the plugin, you can force an EPG update at any time from the SageTV STV by simply loading the plugin configuration screen for this plugin and selecting the **Force EPG** button on that screen.  The button will not be visible if the plugin is unlicensed.