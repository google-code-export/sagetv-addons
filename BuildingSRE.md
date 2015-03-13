# Deprecated!! #

This build howto is deprecated.  If you wish to build SRE from source then follow the [SageAlert](SageAlertBuildFromSource.md) build document.  You can basically follow that document verbatim in order to build SRE from source.

# Purpose #

This document will explain how to setup a build environment for the SRE project. After following the instructions outlined below, one should be able to extract the latest source from SVN and build a zip file suitable for deployment within SageTV. Most users of SRE will simply want to download and install an official release of SRE. Read the [Installing SRE v2.x](InstallingSRE2.md) document for those details.

# Required Tools #

The following tools are required to build the SRE project:

  * Java JRE and JDK
    * Minimum of JRE6 and JDK6 required
    * Downloading the JDK package from Sun will also include the JRE
    * Visit http://java.sun.com/ for downloads and installation instructions
  * Google Web Toolkit (GWT)
    * Minimum 1.5.1 (a.k.a. 1.5RC2); current build.xml file uses v1.5.2
    * The build files expect the GWT to be installed in a certain location. Those locations are:
      * Windows: `C:\gwt-1.5.2-windows\`
      * Linux: `/usr/local/gwt-1.5.2-linux/`
    * If you choose to install to a different location or aren't using the same GWT version as that referenced in the build files (currently 1.5.2) then you will need to edit the build.xml file found in the root directory of the SRE project
    * Visit the [GWT web site](http://code.google.com/docreader/#p%28google-web-toolkit-doc-1-5%29s%28google-web-toolkit-doc-1-5%29t%28GettingStartedInstall%29) for downloads and installation instructions
  * gwtsrwc
    * Grab the latest available version of the gwtsrwc jar file from the [project web site](http://gwtsrwc.googlecode.com).
    * Copy the gwtsrwc jar to `C:\sagelibs\` on Windows or `/usr/local/lib/sagetv/` on Linux; if necessary rename to **`gwtsrwc.jar`** (or create a symlink pointing to the installed jar)
    * **Note**: As of 31 Aug 2008, there is no official release to download, SRE currently builds against alpha versions of the library; if you are trying to build and need this library send me an email
  * SageTV Runtime
    * Copy the `Sage.jar` file from your SageTV installation (`$INSTDIR/JARs/Sage.jar`) to `C:\sagelibs\` on Windows or `/usr/local/lib/sagetv/` on Linux
    * This doesn't _have_ to be kept recent, unless new additions to the API are being used by SRE, it's just used to build; at run time the app will **always** be using the `Sage.jar` from your current SageTV installation
  * GKusnick Studio Tools
    * Download from [here](http://forums.sagetv.com/forums/downloads.php?do=file&id=128).
    * Extract the `gkusnick.sagetv.jar` file from the downloaded zip and place it in `C:\sagelibs\` on Windows or `/usr/local/lib/sagetv/` on Linux
  * Jetty Servlet API
    * If you haven't already, this would be a good time to install the [Jetty plugin](http://forums.sagetv.com/forums/downloads.php?do=file&id=233) for SageTV. SRE requires the Jetty plugin to run (starting with v2.0.0.0).
    * Copy the `servlet-api-xxx.jar` (where xxx is the version number of the jar file installed) from the `SageTV\jetty\lib\` directory to `C:\sagelibs\` on Windows or `/usr/local/lib/sagetv/` on Linux; **rename the file to `servlet-api.jar` or create a symlink**
  * JSON Library
    * Install `json.jar` to `C:\sagelibs\` on Windows or `/usr/local/lib/json/` on Linux
    * Download and build source from [here](http://www.json.org/java/json.zip).
    * Make sure the jar you create is named **`json.jar`** or create a symlink
  * SQLite
    * Download and install the latest (minimum v052) SQLite JDBC driver from [here](http://files.zentus.com/sqlitejdbc/).
    * Copy jar file (i.e. `sqlitejdbc-v052.jar`) to `C:\sagelibs\` on Windows or `/usr/local/sqlite/` on Linux; **rename to `sqlitejdbc.jar` or create a symlink**
  * Subversion (SVN) client
    * The build process requires that the svn client be available on the system path; depending how you install your svn client, this may already be done for you
    * On Windows, I've had success [installing this client package](http://www.sliksvn.com/en/download).
    * On Linux, I've simply installed the binary packages for my distro
  * Ant
    * Download from http://ant.apache.org/
    * Life is easier if the ant command is found on the system path
    * If you're using Eclipse then you may also use the version of ant packaged with it (on Linux I recommend installing a standalone version of ant on the system path)
  * SVNAnt
    * Download [this package](http://subclipse.tigris.org/files/documents/906/32898/svnant-1.0.0.zip)
    * Copy (or symlink) the `svnant.jar` and `svnClientAdapter.jar` files into `$ANT_HOME/lib/`
    * If you're using the version of ant packaged with Eclipse follow [these instructions](http://code.google.com/p/gwtsrwc/wiki/InstallSvnAntInEclipse)

# Optional Tools #

This tools are optional, but may assist you in working with the SRE project.

  * Eclipse
    * Download latest version from http://www.eclipse.org/
    * If you plan to use Eclipse then you **must install the [Subclipse plugin](http://subclipse.tigris.org/) for Eclipse.** It is required to access SVN repositories within the Eclipse IDE.

# Preparing to Build #

Once you've properly configured all of the required tools, you can proceed with attempting a build of SRE zip file.

## Checkout the Project from SVN ##

First, checkout the SRE project from SVN. If you're using Eclipse, you can checkout a new project from SVN. Details on accessing the SVN repository for the project are available [here](http://code.google.com/p/sagetv-addons/source/checkout). Note that the latest version of the code is found in the `/trunk/sre` directory of the repository. Official releases will be available under the `/tags/sre` directory while special branches of the code will be available under `/branches/sre`.

## Run ant ##

Once you have a copy of the project checked out simply go to the root directory of the checkout. There should be a file named `build.xml` in this directory. If not, find that directory. Once you're there simply run ant. The build.xml file is the ant build script that will build the zip file. If you encounter any errors related to missing tools, etc. then review the instructions above. If you receive compile errors then it might just be that trunk is broken at the moment so you may want to try a released version of the code (from `/tags/sre`) and ensure you can successfully build that. When successful, the zip you just built will be found in the zip directory. In Eclipse, right click on the build.xml file and go to "Run as... Ant Build..." You'll then want to select the install target. After a successful build you'll want to refresh your project view to see the newly created zip folder. The zip you just built will be inside that folder. To refresh the project view, click on the project name and press F5.

# Deploying a Built Zip #

To deploy a zip file you've built, follow the directions in [this](InstallingSRE2.md) document.