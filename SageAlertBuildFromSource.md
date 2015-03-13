

# Introduction #

This document outlines the steps required to build the SageAlert plugin from source.

# Supported Build OS #

Currently, the build environment for SageAlert runs on MS Windows XP.  With a few modifications, one should also be able to build on Linux.  Those required tweaks are discussed later on in this build doc.

# Install JDK #

Install a JDK (I use [Sun's](http://java.sun.com)).  Minimum of JDK 1.5.0 required.  Automated project builds use JDK 1.6 (running in 1.5 compatibility mode).

# Install GWT and GXT #

The web UI is built using Google's web toolkit (GWT) along with GXT.  Download and install GWT 2.0.4 from [Google](http://code.google.com/webtoolkit/).  Grab GXT from [here](http://www.sencha.com/products/gwt/).  The ant script expects GWT to be installed at `C:\gwt-2.0.4\` but this can be overridden by specifying `-Dgwt.sdk=/path/to/gwt` when invoking ant.  GXT should be installed at `${sage.libs}/gxt-2.1.1` but can be overridden with `-Dgxt.home=/path/to/gxt`.  SageAlert requires GWT 2.0.x (I currently am using 2.0.4, but any 2.0.x version should work fine) and GXT 2.1.x (I'm using 2.1.1).

# Install Dependencies #

Various jars are required to build SageAlert and the build script expects to find them in ${sage.libs}.  By default, this path expands to `C:/Users/dbattams/sagelibs`, which probably won't work for anyone else, but you can override this path with `-Dsage.libs=/path/to/jars/` on the command line.  You can figure out which jars are needed by looking at app.plugin.xml.in and common.plugin.xml.in and looking at the listed dependencies for SageAlert.  You should also be able to deduce the needed jars by trying to build and fixing the errors.  Eventually I'll list all of the jars in this doc.  Message me if you need some help and I still haven't updated the jar list.

# Install Ant #

SageAlert is built using [ant](http://ant.apache.org).  If you plan on importing the project within Eclipse then you should just be able to use the version of ant included with Eclipse.  If you plan on building outside of Eclipse then you will need to install ant separately.  If installing separately, it is best to ensure that ant is in your system path.

# Install SvnAnt #

The SageAlert build process automatically injects the SVN revision number into the source code at build time making it easy to identify exactly which version of the code is running.  To support this, you must install [SvnAnt](http://subclipse.tigris.org/svnant.html) within the ant installation you'll be using to build SageAlert.  Details on how to install are in the link provided.  Please be aware that SageAlert uses the "JavaHL" client and so you must ensure that JavaHL support is properly installed when installing SvnAnt.

## Add SvnAnt to Eclipse's Ant ##

If you are planning to build within Eclipse please be aware that in order to add SvnAnt to Eclipse's version of ant, you must do so through the Eclipse configuration.  Simply dropping the SvnAnt jars into the plugins folder will not work.

# Optional: Install `gcupload` Ant Task #

If you will not be uploading to the project site, and therefore won't be using the `gcode` build target, then you can skip this step.

If you will be uploading to the Google Code project then you will also need to install the `gcupload` task into your ant installation.  Add the jar found [here](http://code.google.com/p/ant-googlecode/) to your ant installation.

# Checkout Source Code #

Checkout the source code for the project by visiting [here](http://code.google.com/p/sagetv-addons/source/checkout).

# Run the Build #

From the root directory of the checkout (where the `build.xml` file resides), simply run:

`ant app_plugin`

This will build the server application zips and the common jar package.  All the zip files will be dumped in the root directory of your checkout.  Use `clean` target to delete all generated files.  If you are uploading to the project web site then use the `deliver` target to upload the generated packages to Google Code.  **NOTE:** Ensure your local checkout is clean and contains no local modifications prior to running the `deliver` target.  Package names with local mods are **_NOT_** to be uploaded to Google Code (i.e. sagealert-0.0.3.555M.zip).

# Building on Linux #

Building on Linux is as simple as redefining the paths for the various jars needed to compile.  Have a look at the top of `build.xml` to see the various paths that need to be defined for the build.  Then just redefine them all on your ant command line.  To do that, when invoking ant simply add the following to the command line:

`-Dgwt.sdk=/path/to/gwt`

Add a `-D` for each var that needs to be defined (GWT, GXT, sagelibs, etc.).

If you are also uploading to Google Code from Linux, you will have to specify the location of the Google Code project upload settings file via:

`-Dgc.upload.settings=/path/to/settings/file`

The format of the file is intentionally left out of this document.  Speak to a project admin should this need arise for further details.