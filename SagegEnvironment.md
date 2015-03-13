# What is _sageg_? #

At its core, sageg is a series of small scripts and tools I've accumulated that were mainly used during my days of SageTV plugin development.  They provided quick little tools to tweak and query my SageTV servers while developing, testing and/or troubleshooting my plugins, other plugins, or the Sage core.  For example, while debugging a plugin issue, I may need to know the current, realtime value of a SageTV server property.  Since `Sage.properties` may not be current (if it wasn't recently flushed to disk), the only reliable way would be to use the expression editor in Studio or some other bulky mechanism.  Well, with sageg, I just query via the command line:

```
C:\Users\Slugger>sageg getsp sdepg/cacheTTL
Connecting to SageTV server at [192.168.2.146]
7
```

The beauty of sageg is that new commands and tools are added easily by simply writing a new script and saving it in the predetermined location.  Running sageg with no arguments lists all the available commands on the current system:

```
C:\Users\Slugger>sageg
Connecting to SageTV server at [192.168.2.146]
sageg: Required command missing!  Valid commands are:

   getcp: Get a client property: <ui_context> <name>
   getmf: Get media details for a file path
   getos: Display the SageTV server's OS
   getsp: Get a server property: <name>
lnimport: Import video & link to Show: <file> <show_eid>
   setcp: Set a client property: <ui_context> <name> <val>
   setsp: Set a server property: <name> <val>
```

The commands listed above are but a small subset of the commands I've created over the years, but are the only scripts that either didn't need cleaning up or were easy enough to clean up that I feel comfortable releasing the code.  Also, this subset of commands from my environment require no additional third party jars, making installation slightly easier.

Really, I'm more or less providing access to my toolbox here, allowing others to add their own tools to the toolbox easily.  I've had requests for small utilities and scripts in the past, today I show you how I'd add such little utilities to my environment.

# Requirements #

  * SageTV 7.x server with the sagex-services plugin installed, configured and running.
  * Java 1.5 or newer runtime installed on the system you intend to run sageg from; this does **NOT** have to be the same system running your SageTV server, but it must be able to connect to the SageTV server (i.e. no firewall restrictions).  Java **must** be installed before Groovy.
  * Groovy runtime installed on the system you intend to run sageg from; this does **NOT** have to be the same system running your SageTV server, but it must be able to connect to the SageTV server (i.e. no firewall restrictions).  **See the _sageg for Windows_ section below for more details on configuring Groovy for Windows environments.**

# Installation #

I'm assuming sagex-services is already installed and running on your Sage server, do that first if that's not the case.

## Java Installation ##

Download and install Java from [Java's web site](http://www.java.com).  Linux users may wish to install Java from their distro's package manager instead.  Be sure to install Java 1.5 or newer.

## Groovy Installation & Setup ##

Head over to the [Groovy site](http://groovy.codehaus.org) and download the latest installer for Groovy.  If you're on Linux, you may want to install the latest version from your distro's package manager for easier installation.  Nothing older than 1.7, I recommend 2.0 or newer.  Follow the additional platform specific instructions below.

### Windows ###

When downloading your Groovy build, be sure to download a **Windows Installer** version of Groovy installer.  Don't just download the zip package for your platform.  The Windows installer will _easily_ configure various system variables that need to be configured.  You could do it manually, but why bother?

As of this writing, the latest version of Groovy is v2.0.6, but there is no Windows installer build for that version yet.  Instead, grab v2.0.5 Windows installer and use that version.  If there is a newer Windows installer available then go ahead and use that instead.

Run the installer and use all the default values to install until you get to this screen:

<img src='http://wiki.sagetv-addons.googlecode.com/git/sageg_imgs/groovy_inst1.png' />

Ensure all the checkboxes are selected as shown above.  Continue on to the next screen and ensure all the checkboxes are selected as shown below:

<img src='http://wiki.sagetv-addons.googlecode.com/git/sageg_imgs/groovy_install2.png' />

After this screen, continue on with the default values until installation is complete.

To validate everything was configured properly with the installation, open a command window and run the following commands:

```
C:\Users\Slugger>echo println(new Date()) >> test.groovy

C:\Users\Slugger>test.groovy
Sat Dec 29 10:10:06 EST 2012
```

If you don't get the current date printed out when executing `test.groovy` then something went wrong with the install.  Try rebooting first then try again.  If it still doesn't work then review the installation steps.  The Windows shell **MUST** execute `.groovy` files in order for sageg to function properly.

### Linux ###

_**TODO**_

# sageg Download & Installation #

Download the latest `sageg.zip` from this project's downloads section.  Go to the `.groovy` directory under your **home** (`C:\Users\johndoe\.groovy` on Windows or `$HOME\.groovy` on Linux) and unzip the download in that directory, **maintaining the directory structure**.

Next, go to the `\SageTV\JARs\` folder on your SageTV server.  Copy the `sagex-api.jar` file from there and copy it into `$HOME/.groovy/lib/`.  Create the `lib` directory if it doesn't already exist.

Finally, add `$HOME/.groovy/bin` to the PATH environment variable on your system.  For Windows users, this means adding `C:\Users\johndoe\.groovy\bin` to the System wide `PATH` environment variable.  Linux users, just add `$HOME/.groovy/bin` to your system path.

Once all of that is done, open a **new** command window and run `sageg` with no arguments:

```
C:\Users\Slugger>sageg
sageg: Required command missing!  Valid commands are:

   getcp: Get a client property: <ui_context> <name>
   getmf: Get media details for a file path: <file>
   getos: Display the SageTV server's OS
   getsp: Get a server property: <name>
lnimport: Import a video & link it to a Show: <file> <show_eid>
   setcp: Set a client property: <ui_context> <name> <val>
   setsp: Set a server property: <name> <val>
```

If you get output similar to the above then congratulations, sageg is installed and ready to go!  If you get "command not found" or something similar then review the installation instructions.