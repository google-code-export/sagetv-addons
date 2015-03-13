# What is a snapshot build? #

A snapshot build is a build of the project at a given point in time.  Unlike official releases, snapshot builds aren't necessarily tested nor are they guaranteed to function correctly.  The only thing that can be said about a snapshot build is that the build process completed successfully and produced a package that could be shipped to the web server.

# Why snapshot builds? #

Snapshot builds are used by developers to test the latest version of the code or, in the case of a snapshot build failure, a chance to see any failures caused by recent SVN commits.

Snapshot builds are made public for many reasons, the two key reasons being:

  * Allows for more testing to be done by more people, which catches bugs before they're introduced into an official release
  * Allows people to get a build that contains a fix for an [issue](http://code.google.com/p/sagetv-addons/issues/list) that is causing them problems; though the issue may be fixed in SVN, it could be some time before an official release containing the fix is made available

# Where are the snapshot builds? #

All project snapshot builds are available in the project [download area](http://code.google.com/p/sagetv-addons/downloads/list?can=2&q=label%3AQA-Snapshot&colspec=Filename+Summary+Uploaded+Size+DownloadCount).  Snapshot builds are identified by the `QA-Snapshot` label.

# What are the `sre.*.changes` files for? #

Each snapshot build contains two files along with the zip package:

  * `sre.rx.delta.changes`: Contains a list of commits since the last successful snapshot build; allows you to see exactly what changes are in this build and decide if you want to run it
  * `sre.rx.stable.changes`: Contains a list of all the commits since the last stable build (official release); allows you to see exactly what has changed in the project since the last stable release and decide if you want to run it

# Why isn't there a snapshot build every day? #

Two reasons:

  * No new code was checked in since the last snapshot build completed
  * The build is broken (compile errors introduced, etc.) and the automated snapshot build process only delivers the build to the web server if and only if the build reported exactly zero errors

# What snapshot contains the fix for issue xyz? #

Since the issue tracker isn't directly linked to subversion, there is no automatic way to handle this (as far as I'm aware).  Instead, when an issue is marked as fixed a note should be added by the person who closed it denoting which svn revision contains the fix.

If there was no mention of the svn commit number containing the fix then go by the date the issue was closed.  In this case, simply grab a snapshot build dated later than the date the issue was closed.  This isn't a guaranteed method as an issue could be closed before the code is committed to svn (this _shouldn't_ happen, but occasionally it might).  Simply look at the delta.changes file for a snapshot and see if a commit message exists that identifies the issue you're looking to fix.  Just as closed issues should contain the svn revision containing the fix, svn commit messages should also reference issue numbers when the commit fixes an issue (again, this isn't automatic so it's a project convention that is bound to be forgotten periodically).

Once you know which svn revision contains the fix you want, simply grab a snapshot build with a number equal to or greater than that svn revision (snapshot builds are named after the svn revision the build was done under).

# How long are snapshot builds kept around? #

The snapshot builds will be purged after each official release.  Snapshots may also disappear due to disk space needs, etc.