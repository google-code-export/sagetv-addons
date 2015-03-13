# Purpose #

This document describes how to install and run a version of SRE (2.0.0.0 or above).

# Upgrading from v1.x? #

If you're upgrading from v1.x to a v2.x release of SRE then you **must remove the v1.x installation from SageTV by following [these directions](RemoveSRE1.md). DO NOT ATTEMPT TO INSTALL v2.x until you complete these instructions.** If you're not upgrading from v1.x then you can skip this section.

# Install Jetty Plugin for SageTV #

SRE v2.x uses the Jetty plugin as its run time environment. Before going any further in this document, install the [Jetty plugin](http://forums.sagetv.com/forums/downloads.php?do=file&id=233). Once the Jetty plugin is installed and running continue on with this document.

# Upgrade SageTV's `commons-logging` Package #

Starting with v2.4.0, SRE requires an upgrade to SageTV's `commons-logging` jar files.  This is a **requirement** and failure to complete this step will render SRE inoperable.  Follow [these directions](CommonsLoggingUpgrade.md) before proceeding with the SRE installation.  It's actually quite simple to perform this upgrade.

# Download #

Grab the latest official release from the project [downloads section](http://code.google.com/p/sagetv-addons/downloads/list) or grab a [snapshot build](SRESnapshotBuilds.md).

**Ensure you have a v2.x zip file. A v1.x zip WILL NOT work in the Jetty environment (and will probably crash the Jetty server).**

# Deploy #

The reason for the switch to the Jetty environment is because versions of SRE can be installed, removed, upgraded, etc. **without ever having to stop/restart SageTV!** There are some other technical reasons for the switch, but the ability to hot deploy versions of SRE without the need to stop/restart SageTV was one of the key factors.

Unzip the zip file you downloaded in the base SageTV directory (usually **`C:\Program Files\SageTV\SageTV\`**). **Ensure you maintain the directory structure when unzipping!**

That's it! SRE is now installed and running. SRE is deployed on the jetty server using the context `/sre` so to configure SRE visit `http://your_hostname:jetty_port/sre/SRE`

If you've installed the Jetty plugin and SRE on a host with IP address 192.168.0.1 and Jetty is configured for port 8000 then the URL for accessing SRE would be:

`http://192.168.0.1:8000/sre/SRE`

# Undeploy #

If for any reason you need to stop SRE you simply delete (or move) the `sre.context.xml` file from `SageTV\jetty\contexts\`

Deleting (or moving) the context file from the contexts directory will undeploy the web application from the Jetty server. To restart (redeploy) the SRE plugin simply replace the context file back into the contexts directory.

# Restart #

To force a complete restart of the SRE plugin you can update the timestamp of the context file in the contexts directory. This is referred to as "touching" the file on Unix (use the touch command). I'm not aware of a touch command on Windows so the same effect can be achieved by opening the file and simply resaving it, which will also update the timestamp of the file.

# Upgrading #

**16 Nov 2008: The hot deploy functionality of Jetty appears to be working for _most_ users on Windows.  Some users may experience problems upgrading while SageTV is running.  I am personally experiencing this on a WinXP Professional SP2 laptop, but have no problems with the hot deploy on my WinXP Home SP3 server.  If you get errors about files being locked when attempting to upgrade SRE then simply stop SageTV before unzipping the zip package then restart SageTV.**

If you want to upgrade to a different version of SRE simply unzip the zip file in the SageTV directory. As long as the timestamp of the context file is updated then Jetty will undeploy the current version and deploy the newly installed version. **You do not need to stop/restart SageTV to upgrade SRE (v2.x)!**

# Force SSL Connection #

To force all connections to the GUI interface to be secure simply edit the sre.context.xml file (typically found at `C:\Program Files\SageTV\SageTV\jetty\contexts\`) and uncomment the line that sets the `dataConstraint` property.  When you save the changes to the file it will trigger an automatic restart of SRE.  You will have to make this change each time you upgrade/downgrade SRE.

You can still access SRE via the SSL port of Jetty without this change, but the SSL connection is not forced by Jetty unless you uncomment the line in the context file.