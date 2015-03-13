# Purpose #

This document describes the process of uninstalling SJQ v1.x from your system. You **must uninstall** v1.x before attempting to run a v2.x version of SJQ.

# A Little Pain Adds Huge Benefits #

So you've decided to take the plunge and upgrade from SJQ 1.x to SJQ 2.x! I realize that this uninstallation process is a little bit of a burden, but the benefits, I feel, greatly out weigh the pain involved. Please be assured that these steps only need to be completed once and when complete you will no longer need to stop/restart SageTV to install/stop/restart/upgrade SJQ! By using the Jetty server as the new run time environment for SJQ I am able to take advantage of its hot deploy architecture meaning that the applications it runs can be started/stopped/restarted/upgraded/etc. without the need to stop/restart the Jetty server (or SageTV).

# Stop SageTV #

Stop the SageTV server or client where SJQ v1.x is currently installed.

# Remove the Following Files #

Change to the base SageTV installation directory (usually **`C:\Program Files\SageTV\SageTV\`**) and remove the following files:

  * `sqlitejdbc.so`
  * `sqlitejdbc.dll`
  * `JARs\*sjq*.jar`
  * `JARs\*sage-job-queue*.jar`
  * `webserver\webroot\sage\sjqweb\` (delete this entire folder)
  * `webserver\webroot\sjqweb\` (delete this entire folder)
  * `webserver\webroot\sjq\` (delete this entire folder)
**Note:** Some of the above files may not exist on your system (depends on which version of SJQ you're running); if some of the files above don't exist simply ignore them.

The following files should be deleted **only if you're not also running the SRE v1.x plugin:**

  * `JARs\sqlitejdbc.jar`
  * `JARs\json.jar`

**Note:** If you are running SRE v1.x then I'd highly recommend upgrading to v2.x or removing SRE v1.x from your system.  SRE v1.x is no longer supported.

## Optional: Edit link to SJQ in web server plugin ##

You may also want to edit/add a link to SJQ from the web server plugin.  You will want to edit the `webserver\webroot\sage\menu_items.js` file to do this.  To link to the SJQ app simply update the URL to point to the new location (described in [the installation document](InstallingSJQ2.md)).

## Optional: Update web server plugin's `servlets.properties` file ##

You may also want to edit the `webserver\servlets.properties` file and remove the reference to the SJQ servlet (which no longer exists).  Not doing so won't cause any harm other than the web server plugin logging an error at start up saying that it could not init the SJQ servlet.

# Remove Old SJQ Plugin from SageTV #

Edit your `Sage.properties` file (or `SageClient.properties` for client installs) and look for the line that starts with **`load_at_startup_runnable_classes`**. Remove any classes that refer to SJQ. Depending on the version of SJQ you're running that class might be **`slugger.sagetv.sjq.SageJobQueue, net.sourceforge.sagetv.sjq.SageJobQueue, net.sf.sagetv.sjq.SageJobQueue, net.sf.sagetv.sjq.server.SageJobQueue.`**

Only remove the entry for SJQ, keep the remaining entries as is with multiple entries separated by semicolons.

# Install Jetty Plugin #

If you haven't already, this would be a good time to install the [Jetty plugin for SageTV](http://forums.sagetv.com/forums/downloads.php?do=file&id=233). SJQ v2.x uses the Jetty plugin as its run time environment. Part of the installation process for Jetty involves editing your Sage.properties file so you might just as well take care of it all at once. ;)

# Restart SageTV #

Go ahead and restart SageTV. Take a moment to verify that the Jetty plugin is running. By using Jetty as the run time engine for SJQ, you will no longer need to stop/restart SageTV to upgrade/remove/restart/install SJQ! Once you've verified that Jetty is up and running you can continue on with the [SJQ v2.x installation](InstallingSJQ2.md).