

# Introduction #

SageAlert is a plugin for [SageTV](http://www.sagetv.com) that allows your SageTV server and clients to send notifications of events to any number of notification services.

SageAlert supports the sending of notifications to the following systems:

  * Email
  * [Twitter](http://www.twitter.com)
  * [Growl](http://www.growlforwindows.com/gfw/)
  * CSV files
  * Process executor

Users may configure multiple accounts on each supported service and can configure SageAlert to send notifications to as many accounts as desired.`*`

**`*` To send more than one notification per event, send alerts via an SMTP (email) server, or to execute a process in response to alerts requires a license file to unlock the features.  A license file is obtained via a donation to the project.  There is a donate button in the web UI of SageAlert or you can donate by clicking [here](https://www.paypal.com/cgi-bin/webscr?cmd=_s-xclick&hosted_button_id=AQV6A8JXY7XRJ). More details about donating can be found in the [donations FAQ](DonationsFaq.md).**

# System Requirements #

  * Java JRE 1.5.x or newer
  * SageTV 7.0.12 or newer

# Installation #

Starting with v2.0.0, SageAlert is installed via the new SageTV 7.0 plugin manager.  Installing SageAlert from the plugin manager will install all of the required dependencies automatically.

Once you install the plugin via the plugin manager the application is running and is deployed at the `/sagealert/` context in your Jetty server.  You can configure the application by visiting:

`http://your_jetty_ip:your_jetty_port/sagealert/`

e.g. `http://192.168.0.100:8080/sagealert/`

The id/pwd is the id/pwd configured for your Jetty server.

## Force SSL only ##

For the security conscious, you can force all traffic to/from the application to happen over the secure (SSL) port of your Jetty installation.  To do this, open the `C:\Program Files\SageTV\SageTV\plugins\sagealert\SageAlert.context.xml` file and search for the line containing `dataConstraint`.  Uncomment this line, save the file and restart SageAlert (or SageTV itself).  After the restart all traffic will be forced onto the SSL port of your Jetty installation.  **NOTE:** If you do this and Jetty isn't configured properly for SSL access then SageAlert will no longer be accessible.

# Configuration #

Once you can access the app web site you should configure SageAlert by doing the following:

  * First, you must register one or more notification accounts by adding one or more email, Twitter, CSV file, or Growl accounts by visiting the various service tabs.
  * Next, go to the _Alerts_ panel and configure which alerts you want to be notified about.  Simply click on an alert to open its configuration panel, read the description of what it notifies you about and then decide which notification accounts should receive the alert.  After you make your changes be sure to click the **Save** button to save your changes.
  * Optionally, visit the _Clients_ tab to setup aliases for your various SageTV clients and extenders.  These aliases will be used in notification messages in lieu of IP addresses and MAC addresses.  Clients will automatically appear on this tab after they have connected to the server running SageAlert.  You cannot add clients manually, they will be added automatically.

# Support #

Support is available at the SageTV [user forums](http://forums.sagetv.com/forums/showthread.php?t=48574).  Though I do monitor the thread at the user forums, I'd prefer a post to the project's Google Group at:

http://groups.google.ca/group/sagetv-addons

The Google Group allows separate threads for each issue and is easier to search.  However, both locations are monitored and messages posted at either spot will be answered.