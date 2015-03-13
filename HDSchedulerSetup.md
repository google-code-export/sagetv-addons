# Plugin Obsoleted #

**01 Dec 2008:** I was informed today by SageTV support that the next 6.5 beta build (should be 6.5.3) of SageTV will include a change that will always favour HD recordings over SD recordings regardless of the airing time.  This change in the core's scheduler will obsolete this plugin.  I've removed the snapshot builds and will not make an official release of this plugin.  The source code will remain in SVN should anyone want to browse it or build it for use on older versions of SageTV (v6.4 is the minimum version supported by the plugin code).

# Purpose #

This document will explain how to install the HD scheduler plugin along with some quick notes about how it's used and how it works.


# Installation #

The HD scheduler plugin runs in the [Jetty](http://forums.sagetv.com/forums/downloads.php?do=file&id=233) environment.  You must install the Jetty plugin before installing this plugin.

Once Jetty is up and running, simply unzip the zip package in the base SageTV installation directory (usually `C:\Program Files\SageTV\SageTV`).  **Remember to maintain the directory structure when unzipping.**

That's it!  The plugin is now running.  Output is available by visiting:

`http://your_jetty_server/shd/SHD`

A future version of this plugin may include a GUI for setting of options, etc.  Should that happen, the above URL will be where the GUI loads from.

# Removal #

If you need to stop the plugin, simply move the shd.context.xml file from the contexts directory of the Jetty plugin (usually `C:\Program Files\SageTV\SageTV\jetty\contexts\`).  To restart the plugin, simply replace the file.  A restart can also be triggered by updating the timestamp of the context file.

# How It Works #

The basic idea is simple.  When installed, this plugin scans your recording schedule every fifteen minutes looking for scheduled favourites that aren't scheduled to record in HD.

For each one found, it looks for alternate airings that are in HD.  If any are found, the plugin attempts to schedule one of them to record.  The plugin keeps track of what it has scheduled and after the rescheduled airing is done recording the plugin will automatically remove the manual flag for you.

# Limitations/Warnings #

First, this plugin is in its infancy and should definitely be treated as alpha quality.  With that said, I'm running it and it hasn't caused any damage yet, but my point is that it could so if you don't like bleeding edge plugins on your system then I'd stay away from this one for now. :-)

The plugin does **NOT** honour your favourites' priority settings.  Two reasons, first the Sage API offers no way to determine the priority of one favourite versus another.  Even if it did it wouldn't matter, as this plugin would have only used it to break ties in its own scheduling algorithm.  Second, the purpose of this plugin is to get as many (favourite) recordings as possible in HD.  In order to do that, it must ignore your favourite priority rankings.

Finally, **_keep an eye on your recording schedule!_**  As stated above, this plugin is fairly new.  Keep an eye on what it's doing to your recording schedule.  The output of the latest run of the plugin is always available at:

`http://your_jetty_server/shd/SHD`

Read this often, especially when you're unsure of what is happening with your recording schedule.

# Scheduling Algorithm #

Here's some pseudo code showing how the scheduler works in this plugin:

```
List<List<Airing>> rescheduled;

foreach(airing from scheduledRecordings) {
   if(airing.IsFav == true && airing.IsManual == false && airing.IsHD == false) {
      List<Airing> list = allHDAiringsOfShow(airing.GetShow())
      if(list.size > 0) {
         rescheduled.add(list);
      }
   }
}

if(rescheduled.size > 0) {
   /*
      If there are new favourites or old ones we couldn't reschedule then bring in the
      ones we've also rescheduled previously.  Why?  We must reschedule everything every
      time in order to reduce the chance of scheduling conflicts.

      This pseudo function encapsulates a lot of things, but basically what it's doing is
      checking to see if we've rescheduled anything, if so then cancel those recordings
      and put those shows back in the list to be rescheduled again.  We do this to
      minimize the risk of encountering a scheduling conflict.
   */
   cancelRescheduledRecordingsAndAddBackToList(rescheduled);

   /*
      When it's time to schedule HD airings to record we first take the list of lists and
      sort them by smallest list to largest list.  Why?  We're trying to record as many
      HD airings as possible so we schedule the shows with the fewest HD airings first
      followed by the ones with the most HD airings last.  By doing this we expect that
      we'll have the best chance possible at being able to schedule all the shows in HD.
   */
   rescheduled = rescheduled.sortByListSizeAscending();
   foreach(hdAirings from rescheduled) {
      rescheduled = false;
      foreach(airing from hdAirings) {
         if(airing.record() == true) {// Recording scheduled, move on to next show
            rescheduled = true;
            break;
         }
      }
      if(!rescheduled)
         logError("Unable to to reschedule airing.GetTitle() in HD...");
   }
}
```