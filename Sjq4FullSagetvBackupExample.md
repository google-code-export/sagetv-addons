

# Introduction #

The following is a step by step guide for setting up periodic **full** backups of SageTV via SJQv4.

# Target Audience #

This is **an advanced** tutorial.  It assumes you have some SJQv4 knowledge and are quite comfortable working with SJQv4.  Setup of this task requires the use of a **standalone task client** (console or Windows service).  This task **cannot be run from the SageTV plugin version of the task client.**

# Background #

In older versions of SageTV it used to be sufficient to simply backup your `Wiz.bin` and the `.properties` files and you could then use these to restore from a disaster.  Well, with SageTV v7 this is no longer the case.  There are many other files not discussed, but critical to a proper restore of SageTV.  Therefore, it is recommended that users take a complete backup of the entire SageTV application folder for a backup.  Bits and pieces can be replaced as necessary, but a complete restore is only possible from a complete directory backup.

Just trying to restore `Wiz.bin` and `.properties` files will, for example, leave your plugin state invalid and will cause you all sorts of headaches.  In case of disaster, you should be restore an entire app directory backup.

# Must Run from Standalone Client #

I'll be looking to address this in the future, but for the time being this is a limitation of this task.  This is because this task will shutdown SageTV as part of its execution and that will also shutdown the plugin version of the task client.  Therefore, you must run this task from a standalone task client that will not be affected when the SageTV server is shutdown.

# So What Does this Task Do? #

In a nutshell:

  * Run the test script and determine if it's "safe" to shutdown SageTV and proceed with a backup
  * If it's "safe", run the backup script, which does the following:
    * Stop SageTV
    * Backup the SageTV app directory to the chosen target directory
    * Restart SageTV

As you'll see, there are various config options to decide how many backups to keep, etc.  Those are all discussed in the script and I assume you'll config them appropriately.

# Download Groovy Scripts #

Here we go... download the [test script](http://sagetv-addons.googlecode.com/svn/trunk/SJQScripts/full_backup_test.groovy) and the [backup tool script](http://sagetv-addons.googlecode.com/svn/trunk/SJQScripts/full_backup.groovy).  Place these where you usually place your Groovy scripts.

# Configure a Standalone Task Client to Run the Task #

Configure your standalone task client to run this new task.  The scripts assume you're running on the same host as the SageTV server.  This is the only way I've tested things, but the scripts discuss how you might be able to pull this off via remote command execution.

# Setup a Cron Job #

Now setup a cronjob to queue a backup task whenever you want.  Personally, I do a full backup once a week so I have a cron entry that looks like this:

```
# Schedule a full backup every Sunday morning
0 2 * * Sun FULLBACKUP
```

Adjust the task id based on how you set up your system.

# Manually Trigger #

The other nice thing is that you can manually trigger a backup at any time via the SJQ UI plugin.  You might want to do this just prior to an upgrade.

# Run Outside of SJQv4 #

Finally, you could run `full_backup.groovy` as a standalone script using [SageGroovy](Sjq4GroovyIde.md) and just run it periodically via a Linux cronjob or via Windows Task Scheduler.  This would allow you to bypass the need for a standalone task client, if that's something you don't want to install.  If you do choose this route then you will need to merge the test script in with the main script in order to do your "safety" checks before proceeding with a backup operation.  The merge is left as an exercise for the reader.

Happy scripting!