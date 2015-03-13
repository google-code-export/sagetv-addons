# Introduction #

Please **_carefully_** follow the steps below before installing a beta build of SJQv3.  These steps only need to be done the first time you upgrade from SJQv2 to SJQv3.  Upgrading a v3 beta with a newer beta build can be done without having to perform these steps.

# Stop SageTV #

First, you must shutdown the SageTV server completely because you will be backing up your SJQv2 database file.

SJQv3 will perform modifications to your SJQ database file such that the database will be incompatible with SJQv2.  Once your database file is converted for use with v3 you **_cannot_** downgrade back to SJQ v2.x unless you replace the database file with the backup copy you're about to make.

After SageTV has been stopped, go to the root install directory and make a copy of the `sjq.sqlite` file (give it a different name, store it in another directory).  Should you choose to downgrade away from the v3 betas then you will have to replace the database file with this backup.  **_The database file for SJQv3 is incompatible with SJQv2 and there is no automated procedure to reverse the changes - you've been warned!_**

# Increase Jetty's Max POST Request Setting #

Starting with version 3, SJQ's task client is much more aggressive in its caching of task output and will attempt to cache much more output data before flushing it back to the server for logging.  To support this, you must increase the maximum amount of data Jetty will accept in POST requests as the default value of 200,000 bytes is not enough.


  * Stop SageTV completely
  * Open `JettyStarter.properties` (typically found in `C:\Program Files\SageTV\SageTV`)
  * Add the following line: `org.mortbay.jetty.Request.maxFormContentSize=5242880`
  * Save the changes to the file and restart SageTV


Failure to make this change will result is the task client failing to flush output back to the server and will also likely result in the hanging of your task processes.  On the other hand, if your tasks do not produce much output then you may not have a problem despite not making this change.  **Users who plan to run ShowAnalyzer via SJQ should DEFINITELY make this change as ShowAnalyzer is known to produce more than enough output (especially when processing H.264 files) to cause problems; however, it is recommended that ALL users of SJQ make this change prior to your first use.**

A future version of the Jetty plugin for SageTV is expected to contain a suitable value for this setting by default.

# Install SJQ v3 Beta #

Everything else is just like a regular upgrade now.  Download a beta build and install it as usual then restart SageTV.  SJQv3 is at the usual location (`http://jetty:port/sjq/`).  Remember, only a v3 task client can talk with the v3 server so you must also upgrade the task client when upgrading the server to a v3 beta.

## SJQv3 Beta Requires Task Client Upgrade ##

The v3 beta of SJQ server requires a v3 beta task client.  Be sure to upgrade the task client as well.