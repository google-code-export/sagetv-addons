# Introduction #

SageTweet is a plugin for [SageTV](http://www.sagetv.com) that allows your SageTV server and clients to tweet activity and status messages to [Twitter](http://www.twitter.com).

By following your server and client's twitter feeds you can get instant notifications of what your Sage system is doing and instant notification of possible problems with your system.

# System Requirements #

  * Java JRE 1.5.x or newer
  * SageTV 6.5.17 or newer
  * Jetty plugin for SageTV installed, configured and operational

SageTweet requires Java 1.5.0 or newer to run.  Older JREs will error out when trying to run this application.

The SageTweet plugin runs as a J2EE application within the Jetty plugin environment.  You must have the Jetty plugin for SageTV already installed, configured and running before attempting an install of SageTweet.  Please see the Jetty plugin docs for details on how to properly configure Jetty.

SageTweet requires SageTV API functionality only available starting in v6.5.17.  SageTweet will check your SageTV version at startup and if the minimum version requirements are not met then the plugin will not start.

# Installation #

Install the SageTweet app just as you would any other Jetty application.  Download the latest zip package and unzip it in the root of your SageTV installation (typically `C:\Program Files\SageTV\SageTV\`).  **Remember to maintain directory structure when unzipping the file.**  Once that's done the application is running and is deployed at the `/sagetweet/` context in your Jetty server.  You can configure the application by visiting:

`http://your_jetty_ip:your_jetty_port/sagetweet/`

e.g. `http://192.168.0.100:8080/sagetweet/`

The id/pwd is the id/pwd configured for your Jetty server.

## Force SSL only ##

For the security conscious, you can force all traffic to/from the application to happen over the secure (SSL) port of your Jetty installation.  To do this, open the `C:\Program Files\SageTV\SageTV\jetty\contexts\SageTweet.context.xml` file and search for the line containing `dataConstraint`.  Uncomment this line and save the file; it will force a restart of SageTweet and all traffic will be forced onto the SSL port.

Before doing this, you must ensure that Jetty is properly configured for SSL access.  Check the Jetty plugin docs for details on how to do this.  Forcing the app onto the SSL port when not properly configured will cause you all sorts of headaches.  If you're unsure about whether you should do this then you probably shouldn't.

If you do choose to do this, **you must uncomment the dataConstraint line after each up/downgrade of SageTweet** since the context file will be replaced and by default SSL only is not enabled.

# Configuration #

Once you can access the app web site you should configure SageTweet by doing the following:

  * First go to the _Twitter Settings_ tab and register the Twitter accounts you will want to tweet to.  **NOTE:** The Twitter id/passwords you save on this panel are stored in plain text within a properties file in your SageTV directory.
  * Next, go to the _Server_ panel and configure what you want to tweet.  Each type of tweet can be directed to its own Twitter account.  You must register Twitter accounts before they can be selected in the drop down lists on this panel.
  * Finally, do the same for the _Clients_ panel.  If you want to tweet about what shows/music/videos your clients are watching then register the clients here.  The SageTV server can only tweet for itself (local client) and any media extenders that are connected.  If you want SageTV clients to tweet then you must install Jetty and this plugin on each client.

## Getting Client IDs ##

To get a list of the client ids that your installation can tweet for, make sure all of the extenders are connected to the server and visit the following URL:

`http://jetty_ip:jetty_port/sagetweet/ShowConnectedClients`

The output of this web page will give the exact client id strings that are expected when registering a client on the _Clients_ tab.

# My Setup #

Here's a quick overview of how I'm using this plugin.

I've created two Twitter accounts for use by my Sage server, let's call them `my_sage_info` and `my_sage_alerts` - these are not the real feed names, in case you wanted to follow my sage box. ;)  I also have my personal Twitter account, `ddb_db` (this _is_ my real Twitter account, in case you want to follow me) and I follow both of my Sage accounts from my personal account.  The reason I created two accounts is because I turn on SMS notification for the `my_sage_alerts` account and turn it off for the `my_sage_info` account.  I then tweet all important messages (conflicts, low space and system messages) to the `my_sage_alerts` account.  Any messages posted there are also immediately sent to my cell phone.  The rest of the messages are sent to the `my_sage_info` account.  These messages are not sent to my cell phone nor do they need to be (though if you wanted to, you could).

With this setup I get all important messages immediately texted to me and the information messages are just posted to my timeline for review, but aren't cluttering my txt msg inbox.

This is just how I'm doing it, the possibilities are nearly endless!

# Support #

Support is available at the SageTV user forums (post link to support thread here, when created).  Though I do monitor the thread at the user forums, I'd prefer a post to the project Google Group at:

http://groups.google.ca/group/sagetv-addons

The Google Group allows separate threads for each issue and is easier to search.  However, both locations are monitored and messages posted at either spot will be answered.