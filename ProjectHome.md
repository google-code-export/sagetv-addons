# Project Migrated #

**23 Nov 2013:** As of Nov 23, this project has migrated to SourceForge.  All artifacts of this project are now available at the new SF project site: http://sourceforge.net/p/sagetv-addons/

This Google Code project will remain as an archive/snapshot of the project, but any new issues should be opened at the SF site.  All new downloads from this project will be hosted at SF.  All current downloads of all plugins have been copied over to the SF site and are available there.  Older (deprecated) versions were **not** migrated and are only available here until Google decides to remove them.


# Project Goal #
Provide plugins, libraries, and tools to enhance the user experience of [SageTV](http://www.sagetv.com).

# News #
**02 Nov 2013:** After some research, I've decided that I will begin to migrate my projects back to SourceForge.  However, only those projects that are active will be migrated, which won't be many.  Those projects that aren't active will simply be archived here until Google decides to completely gut their project hosting.  If/when that happens, I'll probably migrate the remaining source code.  Why am I doing this?  Google has decided to disable their download features for projects, meaning I can't host plugin downloads from here anymore and I don't like the idea of storing source code at one site and downloads at another.  SourceForge continues to support project downloads and have made many enhancements to their platform since the last time I used it and so I've decided to migrate back.

As plugins are migrated back to SourceForge, I will update the list below.

**sdepg:** Migrated as of 02 Nov 2013.  [source code](https://sourceforge.net/p/sagetv-addons/sdepg/) | [issues](https://sourceforge.net/p/sagetv-addons/issues-sdepg/) | [wiki](https://sourceforge.net/p/sagetv-addons/wiki-sdepg/)

**30 Oct 2013:** Starting today, all new releases of my plugins are no longer available for download via Google Code.  Starting in January 2014, Google will be disabling project downloads.  I've started delivering to alternate locations early.  The downloads section on this site contains any current versions of my plugins and all previous versions.  As updates are made, I will deprecate the older versions, removing them from the download list.  I will try to maintain a list of plugins with newer versions and their new location.  This policy change by Google will likely result in me moving my projects elsewhere, but I'm not ready to do that yet.  For now, I'll just deliver outputs to alternate locations.

**sdepg:** v0.0.5.1 now hosted on SageTV's ftp server; all future releases will be hosted on SourceForge.

**05 Aug 2013:** Licensing has been removed for this project!  All existing plugins that require a license can be unlocked by upgrading the sagetv-addons license server plugin in your SageTV installation.  The update effectively provides a global unlock for all plugins!  Enjoy!

**21 Dec 2012:** The project has migrated to Git for source code management.  Only those plugins and tools that are currently active (or might have a chance of becoming active again someday) were converted to Git repositories.  Everything else remains in the old SVN repository, which can be accessed [here](https://sagetv-addons.googlecode.com/svn/trunk).

# Currently Available #

Please note that, effective immediately, all of the downloads from this project require SageTV 7.0 or later.  Where applicable, links to the older versions of downloads will be provided below, but downloads for SageTV 6.x are no longer supported and will no longer have bug fixes and/or features applied to them.  Those wishing to backfit fixes and features may do so by branching from the tagged version of the code in SVN.

## Schedules Direct EPG Plugin ##
[Obtain License](PluginLicenseStore.md)

An alternative to the SageTV EPG service that grabs its listings data via the [Schedules Direct](http://www.schedulesdirect.org) JSON data feed.  In addition to providing EPG data after the Sage EPG service is discontinued, the licensed version also provides the ability to filter and modify the EPG data as it's being inserted into the Sage database via [Groovy](http://groovy.codehaus.org) scripting.  The licensed version of this plugin also provides support for multiple unique lineups and the ability to force EPG updates on demand via the Sage STV.

#### Public Beta ####

**11 Feb 2013:** The plugin is now available in the Sage repository!

## Sage Recording Extender (SRE) ##
[Obtain License](PluginLicenseStore.md)

A SageTV add on that provides real time monitoring of your live event recordings and automatically extends the recordings as needed to ensure that the entire event is recorded to its completion.  The motivation behind this package is to allow the complete recording of live sporting events - there's nothing worse than recording a game only to have the recording end in the 8th inning or overtime!  This plugin addresses the issue by monitoring the status of the event that's being recording on the Internet and extending the recording as necessary until the event is detected as being over.

<img src='http://lh4.ggpht.com/_zGz9YRyqnJ4/TImgnMtKANI/AAAAAAAAACI/FnZWIOiRPaM/s800/sre3_0.jpg' />

#### v4.0.0 Currently in Beta ####
**21 Dec 2011:** SREv4 is currently in beta, but should be considered stable for monitoring recordings.  SREv4 requires SageTV v7.0 or later and can only be installed via the SageTV Plugin Manager.  Watch the plugin manager for updates as new beta builds will be released often with no official announcements.

The last release of SRE for SageTV v6.x can be downloaded [here](http://sagetv-addons.googlecode.com/files/sre-2.4.3.789.zip).

## Sage Job Queue (SJQ) ##
[Obtain License](PluginLicenseStore.md)

A SageTV add on that provides the ability to process your SageTV media files based on any number of criteria.  For example, you can setup rules to have SJQ process your TV recordings only if the watched flag has been set in SageTV, or only if the recording is in HD and so on.  SJQ allows you to decide what to do to which recordings based on the contents of the recording as stored in SageTV.

<img src='http://tmiranda.googlecode.com/svn/trunk/SJQ/ScreenShots/SJQ-MainMenu-RegisterClient.JPG' />

The v4.x stream of SJQ provides many new features.  Of significance:

  * Provide a true, robust scripting environment for your task automation needs
  * Move to an event based task queuing model
  * Redesign of the client/server architecture
  * Add ability to schedule periodic task execution
  * A new STV UI plugin that replaces the web UI
  * Many other bug fixes and enhancements

Please take the time to read over the [SJQv4 User's Guide](Sjq4UserGuide.md), which will help you become familiar with all of SJQv4's new features.

#### v4.0.0 Currently in Beta ####

**12 Dec 2010:** SJQ v4.0.0 is currently in beta.  **Please read the release notes of the plugin before installing!**

The last version of the SJQ server for SageTV v6.x can be downloaded from [here](http://sagetv-addons.googlecode.com/files/sjq-3.0.1.704.zip).

## SageAlert: SageTV Event Notification System ##
[Obtain License](PluginLicenseStore.md)

SageAlert allows you to receive notification of significant SageTV events via email, Twitter, and/or Growl.

<img src='http://lh6.ggpht.com/_zGz9YRyqnJ4/TImePOGI20I/AAAAAAAAABk/7XhwLNTzG3c/s800/sa001.jpg' />

#### v2.0.2 Released! ####

**16 Jun 2011:** SageAlert v2.0.2 final (2.0.2.1550) has been released!  v2.x requires SageTV v7.0 or later and can only be installed via the SageTV Plugin Manager.

The last version of SageAlert for SageTV v6.6 can be downloaded [here](http://sagetv-addons.googlecode.com/files/sagealert-1.0.2.781.zip).