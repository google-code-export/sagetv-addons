# Introduction #

New to SJQv4 is the ability to schedule arbitrary tasks on a regular interval.  To facilitate this feature, SJQv4 introduces the SJQv4 Crontab.

The crontab is based on the [cron4j project](http://www.sauronsoftware.it/projects/cron4j/) and provides a simple way to add tasks to the queue on a regular basis.  Between the crontab and task pretests, you can schedule conditional tasks to run a regular interval.

# The crontab file #

The heart of the SJQv4 crontab is the `crontab` file.  This file is located at `/SageTV/plugins/sjq/crontab` on the Sage server running the SJQ plugin.  In the same directory is a file called `crontab.ref`, which is a reference file that provides some example entries.

The crontab file contains your task schedule.  Tasks are inserted into the SJQ task queue based on the schedule rules you provide in this file.

Each line has the following format:

```
min hr dom mth dow taskId [var=val ...]
```

Where the first five fields are the date/time when the task should be added to the queue and the sixth field is the taskId name to add to the queue.  The rest of the line are optional var=val pairs of environment metadata to attach to the task.

The exact format of the time/date fields is described here:

http://www.sauronsoftware.it/projects/cron4j/api/it/sauronsoftware/cron4j/SchedulingPattern.html

So if you wanted to schedule a REBOOT task once an hour, you'd do it like this:

```
0 * * * * REBOOT
```

That line means schedule a REBOOT task in the queue at the top of every hour (12am, 1am, 2am, ... 11pm).  If you wanted to do it every 30 mins:

```
*/30 * * * * REBOOT
```

Every two hours on the weekends:

```
0 */2 * * Sat,Sun REBOOT
```

The link above has more details and examples.  Bottom line: it's pretty flexible. :)

Then just make sure you have a task client configured to handle tasks of type `REBOOT` and then you're good to go.