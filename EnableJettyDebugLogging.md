## Enable Jetty Debug Logging ##

During the course of debugging problems with a plugin from this site, you may be asked to provide Jetty debug logs.  Unfortunately, on Windows, all the output from Jetty doesn't make its way into the SageTV debug logs so in order to get all the debug info needed to diagnose some problems, you need to enable additional logging with the Jetty plugin.

**First, stop SageTV completely.**

Next, open `C:/Program Files/SageTV/SageTV/Sage.properties` and find the line that starts with `jetty/jetty.configfiles`

Add the jetty-logging.xml config file to the list of config files used by Jetty.  Your new value should look something like this:

```
jetty/jetty.configfiles="C\:\\Program Files\\SageTV\\SageTV\\jetty\\etc\\jetty.xml" "C\:\\Program Files\\SageTV\\SageTV\\jetty\\etc\\jetty-logging.xml"
```

**NOTES:**

  * There may be other config files included (such as jetty-ssl.xml if you have SSL support enabled) and that's fine, just make sure the jetty-logging.xml file is added to the list.
  * Each individual file is surrounded with double quotes

Save the changes to the file and restart SageTV.  After the restart you should notice a new file in `C:/Program Files/SageTV/SageTV/jetty/logs/` called `yyyy_mm_dd.stderrout.log` where the `yyyy_mm_dd` is replaced by the current day's date.  Reproduce your issue with my plugin then check this log file for the output you're suppose to be seeing (typically I'm looking for stack traces).  If you see it, then send it back to me as directed for analysis.

**NOTES:**

  * This config change redirects all debug output from SageTV and Jetty to this new log file in the `jetty/logs` path.  No output will be written to `sagetv_0.txt` while this debug feature of Jetty is enabled.  The format of the log files are different and SageTV support is not fond of receiving debug logs in the Jetty format so you will probably want to reverse these changes as soon as you get the output I'm looking for.  To reverse these changes, **stop SageTV**, edit the `Sage.properties` file and remove the `jetty-logging.xml` config file from the `jetty/jetty.configfiles` list then restart SageTV.