# Functional Description of SRE #

## Overview ##

**Sage Recording Extender (SRE)** is a web application plugin for the [SageTV PVR system](http://www.sagetv.com/). With SRE, you can monitor certain types of recordings and have them automatically record until the _event has completed_ regardless of the program data in the EPG. This means that if you're recording a baseball game you won't miss the late innings or if you're recording the NHL Stanley Cup playoffs then you won't miss any of the game that goes into triple overtime.

## Details ##

In this version of SRE, the following types of recordings will be monitored when SRE is enabled:

  * NHL Hockey
  * MLB Baseball
  * NBA Basketball
  * NFL Football
  * College Football (Division I-A and Division I-AA only)
  * College Basketball (Division I Men's only)
  * Women's College Basketball (Division I only; starting with v2.1.2.356)
  * Men's and Women's Olympic Ice Hockey (Vancouver 2010 Olympics; v2.4.1.774+)

**NOTE:** The NASCAR monitor was disabled starting with v2.1.4.478 as the data source became unreliable.

When an active recording matches one of the above titles SRE will check the status of the event and based on its findings will modify the recording time. If an event is over before its scheduled end time SRE can optionally end the recording early in order to free up a tuner as soon as possible. Ending recordings early is disabled by default and therefore SRE will only ever extend recordings for events that run longer than their scheduled time in the EPG unless you switch the setting manually.

In order to monitor and extend events, the Airing's associated Show object must have an episode string that uniquely identifies the event. In the case of sporting events this means the episode data must identify who's playing in the game. If the episode information for the event is not sufficient to properly monitor the event then SRE will not modify the recording in anyway. For example, if the episode data for an MLB Baseball recording is "Teams TBA" then SRE will not be able to modify the recording since it does not know which game to monitor the status of. In this case, SRE will do nothing and the recording will simply record until the end of the allotted time in the EPG. Also, if the event can be identified, but SRE is unable to access the event's status (Internet access is down, IOException thrown, etc.) then SRE will also not modify the recording times unless safety net extensions are enabled.

## Plugin Features ##

### SRE Will Not Monitor Live TV ###

SRE will never modify live tv recordings.

### SRE Will Not Monitor IR Recordings ###

SRE will never modify Intelligent Recordings.

## Plugin Limitations ##

### SRE May Not Record Entire Event ###

**It is possible that SRE can extend a recording for some time, but perhaps not until the game is completely over!** Consider the following example. You're recording a baseball game that is listed in the EPG from 13:00-16:00. At 15:58 SRE sees the game isn't over and so extends the recording time to 16:05 and will check again at 16:03. At 16:03 SRE wakes up and attempts to check again, but your DSL connection has temporarily dropped or the ESPN.com web site (source for status information) is not responding. Since SRE couldn't make a proper determination about the event it does nothing to the recording times and will sleep until 16:08. However, at 16:05 the recording will stop even though the game may or may not be over. **Starting with v1.2.0, this scenario can be avoided by enabling safety net extensions.**

### SRE May Record Too Long ###

**It is possible that SRE can extend a recording well beyond the event's completion!**  Using the same baseball game from above, if the ESPN.com web site (source for status information) is not properly updated to signify the game is over then SRE will constantly calculate the game isn't over and will continue to extend the recording until the maximum extension length time is exhausted. This situation may also arise if the parser code being used in SRE contains a bug. By default, SRE will only extend recordings a maximum of eight (8) hours. You may want to modify this setting to something lower. I'd suggest that 5 or 6 would be the lowest to go as some NHL Stanley Cup playoff games have been known to run an extra 3-4 hours. SRE allows this setting to be in the range of 1-16 hours. **Please be aware that SRE may, _correctly or incorrectly_, extend an active recording by many hours!** In such cases you should be aware of the resource utilization (extra disk space, extended use of a tuner, scheduling conflicts generated as a result of long running events, etc.).

### SRE Converts Recordings to Manual Recordings ###

A side effect of modifying recording times for favourite based recordings is that the recording is turned into a manual recording. Since manual recordings always have a higher priority than favourite rules, extending a recording with SRE may cause undesired scheduling conflicts if SRE were to extend a recording for a long period of time. If SRE may extend a recording long enough to possibly cause you to miss another recording that you don't want to miss then you should temporarily disable SRE event monitoring via the configuration tab.

### SRE is NOT a Fail Safe Process ###

Given the above limitations, it's clear to see that SRE is not a fail safe way to ensure that live events are always recorded until they're over. However, more times than not SRE will record extra innings/overtime when SageTV would otherwise not be able to. To date, SRE has been 92% effective in recording live events in my testing. The 8% that failed can all be attributed to the above limitations. Specifically, the only causes of failure during my testing were temporary Internet outage and a bug in the SRE parser during development. Starting with v1.2.0, the former is now preventable (though not 100% effective) via safety net extensions and the latter is monitored by running a daily cronjob that tests all of the parsers in SRE and when they fail I get an instant email notification. Bugs in the parsers or obsoleted parsers caused by a change in the source data are usually fixed very quickly. Though I'll never say SRE can be 100% effective, I definitely find it beneficial and better than nothing at all.

# System Requirements #

## JRE 1.5.0 ##

A minimum of JRE 1.5.0 is required.

## SageTV 6.x ##

SRE has been tested on SageTV 6.6.1; any recent version from the 6.x stream of SageTV should work fine.

## Jetty Plugin ##

SRE (v2.x) runs under the [Jetty plugin](http://forums.sagetv.com/forums/downloads.php?do=file&id=233) environment. You must install the Jetty plugin before attempting an installation of SRE.

# Installing SRE #

Please read the [installation doc](InstallingSRE2.md) for details on how to install SRE.

# Acceptable Title/Subtitle Combinations #

Depending on the type of event being monitored, the subtitle used to describe the event will be slightly different.
For all of the professional sports (NFL, MLB, NBA, NHL) the format of the subtitle is not all that strict.  For NCAA monitors, the subtitle is much more restrictive. You must use the school name and the school nicknames cannot be used at all. EPG data from SageTV (Zap2It) follows this restriction and you must also follow it when creating overrides.
The following table lists the title and subtitle requirements for all monitors.
| **Event Type** | **Required Program Title** | **Subtitle Requirements** | **Notes & Exceptions** | **Examples** |
|:---------------|:---------------------------|:--------------------------|:-----------------------|:-------------|
| NFL football | **NFL Football** OR **Football de la ligue nationale** (French) | Team A at|vs.|à Team B | Must use city for all teams except for Giants and Jets| Miami at New England; Giants at Houston |
| NHL hockey | **NHL Hockey** OR **Hockey de la ligue nationale** (French) | Team A at|vs.|à Team B | Must use city for all teams except for Rangers and Islanders| Edmonton at Toronto; Islanders at Ottawa |
| Major League Baseball | MLB Baseball | Team A at|vs. Team B | Must use city for all teams except for Cubs, White Sox, Yankees, Mets, Angels, Dodgers | Yankees at Toronto; Minnesota at Seattle |
| NBA basketball | NBA Basketball | Team A at|vs. Team B | Must use city for all teams except for Lakers and Clippers | New York at New Jersey; Clippers at Lakers |
| NCAA football (Div I-A and I-AA only) | College Football | School A at|vs. School B | Must use school names only **without school nicknames**; see ESPN.com note in NCAA b'ball below | Syracuse at Notre Dame; USC vs. Washington |
| NCAA basketball (Div I men's only) | College Basketball | School A at|vs. School B | Must use school names only with **no nicknames**; visit http://sre-maps.appspot.com to confirm how a school is listed on the source web site (i.e. ESPN uses USC for Trojans, but sometimes the EPG data will use Southern California which will prevent SRE from monitoring) | Louisville at Syracuse; Boston College at Princeton |
| NCAA Women's basketball | Women's College Basketball | School A at|vs. School B | Must use school names only with **no nicknames**; visit http://sre-maps.appspot.com to confirm how a school is listed on the ESPN web site | Rutgers at Syracuse; UConn at Tennessee |
| Olympic Ice Hockey (Men) | Olympic Men's Ice Hockey | Country A vs. Country B | See http://sre-maps.appspot.com for listings on how teams are being identified in source feed; see [this post](http://forums.sagetv.com/forums/showthread.php?p=408273&postcount=390) in Sage user forums for further details | CAN vs. USA |
| Olympic Ice Hockey (Women) | Olympic Women's Ice Hockey | Country A vs. Country B | See http://sre-maps.appspot.com for listings on how teams are being identified in source feed; see [this post](http://forums.sagetv.com/forums/showthread.php?p=408273&postcount=390) in Sage user forums for further details | RUS vs. CAN |

**NOTE: Support for monitoring of French broadcasts (where noted above) requires build 766 or above.**

**NOTE: All monitoring of Olympic ice hockey will require manual overrides to the program title.  This was done on purpose.  Read the referenced Sage user forum post above for details on why this decision was made.**

# Safety Net Extensions #

Starting with v1.2.0, SRE includes a feature called _safety net extensions_. When enabled, SRE will not automatically ignore event monitors that are in an error state or marked as invalid. Instead, if at some point during the life of the recording SRE was able to load a valid event monitor for the recording then SRE will consider the event still in progress and act accordingly instead of ignoring the monitor and doing nothing.
This feature addresses the situation where a temporary Internet outage or a temporary outage at the web site providing status information could cause a premature ending of a recording. Instead, with safety net extensions, this temporary outage is identified as such and the recording will be extended as necessary.
The side effects of this feature include the possibility of the event being extended well beyond its ending. This would occur if there was a long Internet outage or a long outage at the source web site during a recording. Also, if by some chance the source web site were to change the format of its pages and therefore making the SRE parser obsolete it would trigger the safety net and the recording would be extended up to the max extension length setting in SRE, which is currently defaulted at 8 hours.
Because of the side effects introduced by safety net extensions, this feature can be disabled via the configuration tab. By default, safety net extensions are enabled. To disable them simply disable safety net extensions on the configuration tab.

# Default Event Padding #

Starting with v1.3.0, SRE includes the ability to specify a default padding for all recordings monitored by SRE. With a default padding, your events will be recorded beyond the EPG listing even if SRE is unable to properly monitor your event (due to an Internet outage, status source outage, etc.).
The default padding setting is only used if SRE is **never** able to get a valid monitor for an event. In this case, SRE will simply pad the recording using this value. Once SRE is able to get a valid monitor for the event just once then this setting is ignored and the safety net extension settings apply.
The default value for the event padding is zero (0) minutes (i.e. not used). You can modify the default padding via the configuration tab.

# Overriding Airing Details #

Starting with v1.4.0 you can override the title and subtitle of programs to be recorded. This allows you to tell SRE to use a different title/subtitle when fetching a monitor for a recording.
For example, if your EPG says that the NFL game showing on CBS is Pittsburgh at Buffalo, but instead they're actually showing New England at Miami (perhaps due to a blackout) then you simply tell SRE the new subtitle (replacing 'Pittsburgh at Buffalo' with 'New England at Miami'). After doing this SRE will then monitor the New England at Miami game for the recording instead of the game listed in the EPG.
Overrides are defined from the overrides tab.  From there you simply double click on a title or subtitle to bring up the context menu for a recording.  Select "Create Override" to create an override for the selected recording.  Defined overrides are highlighted in orange.  Overrides that are known to be good and valid are also displayed in **bold** text.  To remove an override simply double click on the row and select remove override. You can also double click on a row to view the details of an airing as stored in SageTV. Removing an override will reset the row back to the values as defined in SageTV.
Starting with v1.5.0, defined overrides that do not create a valid monitor will be ignored and removed. For example, if you create an override and use a title that is not monitored by SRE then SRE will simply ignore the monitor and remove it. The same thing will happen if you create an override and set the subtitle to a game that is not being played. For example, you override an NHL airing on Nov 1 and set the subtitle to 'Rangers at Edmonton', but the Rangers and Edmonton are not playing on Nov 1. SRE will notice this fact and cancel the override.

# Remove Manual Recording Flag for Monitored Favourites #

Starting with v1.7.0, you can tell SRE to remove the manual recording flag for any favourite recording that was monitored and modified by SRE. Simply enable or disable the option from the configuration tab. By default, this option is turned on.
Anytime SRE modifies the recording times then that recording automatically becomes a manual recording in SageTV. Manual recordings are treated differently by SageTV when it comes to things like automatically deleting recordings, etc. If you want your monitored favourites to be treated as favourite recordings by SageTV then you should keep this setting enabled in SRE.

# Ignore Recording if Next Airing on Same Channel is Scheduled to Record #

Starting with v2.1.0, you can tell SRE to ignore a recording and not monitor it if the airing following it is also scheduled to record **on the same channel.**  By default, this option is turned off and will need to be enabled from the configuration tab.  This setting is useful in the case where you have scheduled back to back games to record on the same channel and don't mind if the end of the first game ends up as the start of the second recording.  Use of this feature can save the use of two tuners recording the same channel.

# SageTV Configuration Settings #

The following explains the various settings available on the configuration tab of the SRE GUI.

  * **Plugin state:** Determines if SRE will monitor recordings.  When set to off SRE will not monitor any recordings.  Please note that even when set to off the worker thread will still wake up every five minutes (or whatever the frequency is set to) and the last run stamp on the About tab will continue to update.
  * **Run frequency:** How often, in minutes, SRE should wake up and monitor active recordings.  Must be at least two minutes; five (the default value) seems to be an appropriate value to use.
  * **End recording early:** When turned on, SRE will end recordings early if it notices the event has ended early.  SRE only starts to check the status of events when there is 30 minutes or less remaining in the scheduled recording time.  Therefore, SRE will never end a recording more than 30 minutes early when this setting is turned on.
  * **Maximum extension time:** The maximum number of hours a recording will be extended by SRE.  Be careful not to set this too low as some events (especially NHL playoffs) are known to go on for 3-4 hours beyond the EPG data.  If you're concerned about the extended use of a tuner or the extra disk space this setting could consume then I'd recommend setting it no lower than 5, however SRE will accept values between 2-16 for this setting.
  * **Default padding:** The amount of time, in minutes, that SRE will pad a recording when it is unable to find a valid monitor for it.  Please note that once SRE is able to get a valid monitor just once for an event then this setting is **ignored**.
  * **Safety net:** When on, SRE will apply the safety net extensions for all event monitors.  Please see the section above on safety net extensions for more details about how this settings affects monitored recordings.
  * **Remove manual flag for monitored favourites:** When on, SRE will remove the manual recording flag for all monitored favourites.  Please see above for a more detailed description of what this feature does.
  * **Do not monitor an event if the airing following it is also scheduled to record:** When turned on, this setting tells SRE to ignore monitoring an event if the airing following it is scheduled to record on the same channel.  By default this setting is turned off.
  * **Exit URL:** Set this value to the URL you want the exit link at the bottom of the application to send you to when clicked.  The value entered should be a valid absolute or relative URL.

# Receiving Alerts #

SRE now supports notifications via [http://sagetv-addons.googlecode.com](SageAlert.md).  SRE can notify you via SageAlert when it's recording an event or when an event needs an override.  To enable this feature you must first install and configure SageAlert and then configure SRE to use it.

# Finding Override Information #

If SRE tells you an override is required for an event then you can visit http://sre-maps.appspot.com to determine the required override values for a given event.  See [this page](SreEspnBottomLine.md) for details on how to compute an override value for an event.

# Adding Overrides to Global Map #

Starting with v2.4.0, SRE uses the [sre-maps](http://sre-maps.appsot.com) web application to store all of the mappings for team names.  Users are encouraged to update this map with overrides as they create them.  When you create a valid override in the SRE GUI you will be prompted to add the override to the online global map.  Doing so makes the override immediately available to all SRE users and also prevents you from having to submit the same override in the future.  More details about the online override map can be found at the bottom of [this page](SreEspnBottomLine.md).

# Enabling Debug Tab #

To enable the Debug tab in SRE, simply append "?dbg=1" to the URL used to load the application.  This will tell SRE to load the debug tab.  **Be careful when modifying the settings on the debug tab as incorrect modifications could severely mess up your recording behaviour in SageTV.**  Typically, most users shouldn't ever have a need to load nor use the debug tab in SRE.  Its main purpose is to provide debug information and testing environments for testers and developers of the SRE project.

# Support, Bug Reports and Feature Requests #

If you're having problems with SRE then I'll be more than happy to try and assist you. If you're having problems with the functionality of SRE or you think you've found a bug with SRE then I'll likely ask you for the debug output from SRE. All output from SRE is viewable in the Log tab. Output from the last seven (7) days is kept.
If you have found a bug or would like to request a new feature, please add an entry to the project tracker found [here](http://code.google.com/p/sagetv-addons/issues/list).
General discussion and support questions about the SRE plugin can be posted on the SageTV forum thread found [here](http://forums.sagetv.com/forums/showthread.php?t=24460).