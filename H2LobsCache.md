# Does this apply to me? #

You should read this document **only** if the following applies to you:

  * You notice that there is a directory called `C:\Program Files\SageTV\SageTV\.lobs.db\` on your system and its contents consumes many GBs of space (substitute path to SageTV root directory, as necessary).

If this does not apply to you then **stop**.  You should not ever see large disk usage by the directory `C:\Program Files\SageTV\SageTV\plugins\sjq\sjq4.lobs.db\` since the SJQv4 engine constantly cleans this directory.  If you see this directory growing unusually large then report that as an SJQv4 bug immediately.  The `...\plugins\sjq\sjq4.lobs.db\` directory is where the actual SJQv4 database resides.  Modifying the contents of this directory **will corrupt your SJQv4 database.**

# Why is this happening? #

SJQv4 uses the H2 database and there are issues with the H2 network client where it will cache LOB columns locally, but never properly cleanup the cache.  Over time this cache can grow rather large.  For most users, this cache will be stored on a smaller OS drive and can cause havoc as it starts to fill the disk.

# How do I fix this? #

Simple, actually.  Stop SageTV and delete the contents of the `C:\Program Files\SageTV\SageTV\.lobs.db\` directory.  Do **NOT** do this while SageTV is running.

Additionally, check your `SageTV\JARs\` folder and look for h2 jar file (i.e. h2-1.2.145.jar).  If yours is older than 1.2.145 then upgrade the sagex-h2 plugin on your system to get the latest H2 jar file.  It _appears_ the client caching problem is fixed in 1.2.145.  I can't confirm it, but I haven't had any cache files piling up on my system since upgrading.

If the upgrade doesn't fix you up then you will have to monitor this directory and clean it up periodically.  One could even write an SJQv4 script to monitor this situation for you and generate a SageTV system message when the size of the directory gets too large. :)

**IMPORTANT:** Do **NOT** touch the contents of `C:\Program Files\SageTV\SageTV\plugins\sjq\sjq4.lobs.db\`  Modifying the contents of this directory will corrupt your SJQv4 database.  If this directory starts growing unusually large then report it as an SJQv4 bug - this directory is maintained by SJQv4 and should never grow too large (unless you change the task log output settings to retain them for a long period of time).

# Why can't SJQv4 monitor the .lobs.db directory as well? #

This directory is a cache directory used by the H2 tcp client.  SJQv4 knows nothing about it and can't modify it at runtime because it will corrupt your database connections to the SJQv4 server.  The fact that the cache is not cleaned up is an H2 bug and needs to be addressed at the H2 level, not the SJQ level.