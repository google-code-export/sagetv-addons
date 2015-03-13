# Does NOT Apply to SageTV v7! #

This document should be ignored **completely** if you're running SageTV v7.  There is no need to upgrade/modify commons-logging if you're running SageTV v7.

# Introduction #

Many of my plugins use 3rd party libs that rely on Apache's commons-logging library.  Recently, some users have hit problems with the Java ClassLoader not properly loading these dependencies.  A fix has been devised, but requires users to upgrade the version of commons-logging used by SageTV.  Follow the steps below to upgrade SageTV's version of commons-logging.

# Howto: Upgrade SageTV's Version of `commons-logging` #

  * Stop SageTV completely
  * Remove the following jar files from `SageTV/JARs`
    * `commons-logging.jar`
    * `commons-logging-api.jar`
  * Download commons-logging-1.1.1 from [here](http://apache.parentinginformed.com/commons/logging/binaries/commons-logging-1.1.1-bin.zip).
  * Install only the following jars from that zip into `SageTV/JARs`
    * `commons-logging-1.1.1.jar`
    * `commons-logging-api-1.1.1.jar`
  * Restart SageTV

# Versions of Plugins on this Site that Require this Upgrade #

The following versions of plugins on this site will **NOT** function properly without following these instructions:

  * SRE v2.4.0.743 or greater
  * SJQ v3.0.2.x or greater
  * SageAlert v1.0.2.x or greater