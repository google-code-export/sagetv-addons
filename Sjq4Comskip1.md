# Introduction #

This document will guide you through a complete example of how to setup comskip.   This is only one way to do this, there are many other ways to accomplish this task in SJQv4.  As a matter of fact, you'll find there are many different ways to do the same thing in SJQv4.  This is the advantage of moving to a proper, complete scripting environment for SJQv4.

# Separating SD and HD Comskip Tasks #

My first requirement is that I needed to separate SD and HD tasks.  Processing H.264 video is way more taxing on my system that processing MPG2 so I want to separate the two types of tasks.  So for this reason, I have two different comskip tasks defined in my setup: "SDCOMSKIP" and "HDCOMSKIP".  So for any recording I want comskipped I will actually queue up an SD and HD comskip task and then one of them will be skipped and the other will run, as desired.

# Configure the HDCOMSKIP and SDCOMSKIP Tasks #

First, you need to configure one or more task clients to perform these tasks.  This document assumes you've already installed and configured the engine and at least one task client.  See the [User Guide](Sjq4UserGuide.md) if that's not the case.

_**NOTE:** You must have the SJQv4 GUI plugin installed to perform the following actions as described in the document._

Go to **_Setup > SJQ > Show Clients_** and select the client you wish to configure.  Click on **_Create New Task_**.  The following screen shot shows my setup.  You can copy everything you see, but obviously some paths will have to be changed for your setup.

<img src='http://sagetv-addons.googlecode.com/svn/trunk/sjq4/media/images/wiki_comskip1_add_sd_task.jpg'>

<ul><li><b>Executable:</b> The path to your comskip exe.  I use ShowAnalyzer, you can use whatever you like.<br>
</li><li><b>Executable Arguments:</b> The variables you see there come from <a href='Sjq4Metadata.md'>here</a>.  It is important to realize that <code>$SJQ4_PATH</code> is the path of the file <b>as reported by the SageTV core.</b>  If you are running a task client on a machine other than your SageTV server  and the path is not UNC then you <b>CANNOT</b> use the <code>$SJQ4_PATH</code> variable.  Instead you will have to wrap your task in a script and convert the <code>$SJQ4_PATH</code> variable manually.<br>
</li><li><b>Test Script:</b> You can download the test script from <a href='http://sagetv-addons.googlecode.com/svn/trunk/SJQScripts/'>here</a>.  Copy it and configure this path appropriately for your system.<br>
</li><li><b>Test Script Args:</b> For SD recordings, the file should have a <code>mpg</code> extension.<br>
</li><li><b>Max Instances:</b> How many SDCOMSKIPs do you want to run simultaneously?  I've chosen two for my setup; configure accordingly.<br>
</li><li><b>Max/Min Return Code:</b> ShowAnalyzer, like most programs, returns zero on success and any other value means failure.  <code>comskip.exe</code> returns zero OR one on success.  Configure appropriately.<br>
</li><li><b>Max Time:</b> Tell SJQ how long this task can run before automatically killing it.  This value is in seconds.  <b>Do not use the run time ratio setting; it's currently not implemented.</b>
</li><li><b>Resources:</b> How many resources does this task require?  All task clients have exactly 100 resources by default.  Tasks only run if there are enough free resources to run them.<br>
</li><li><b>Schedule:</b> When can this task run?  <code>ON</code> means 24x7, <code>OFF</code> means never (i.e. disabled) or you can provide a crontab schedule string for this task, which gives very flexible, powerful scheduling capabilities for your tasks.  Details on crontab values can be found <a href='http://www.sauronsoftware.it/projects/cron4j/api/it/sauronsoftware/cron4j/SchedulingPattern.html'>here</a>.<br>
</li><li><b>Max Time Ratio:</b> Currently not implemented.</li></ul>

Save the SDCOMSKIP task and create a new one, HDCOMSKIP.  Basically the same thing except replace the <code>mpg</code> arg with <code>ts</code>.  You should now have a task client configured to run both <code>SDCOMSKIP</code> and <code>HDCOMSKIP</code> tasks.  Now you're ready to queue up tasks to be executed.<br>
<br>
<h1>Queuing the Comskip Tasks</h1>

I've decided to queue up comskip tasks by attaching them to favourites.  The advantages of this approach are:<br>
<br>
<ul><li>I only attach comskip tasks to favourites I know will have commercials; favourites recording from HBO, movie channels, PPV, etc. will never queue up comskip tasks<br>
</li><li>I do not need to create a pretest to check which channel the recording is coming from to decide if I need to comskip the recording.</li></ul>

Disadvantages:<br>
<br>
<ul><li>Manual, IR, and live tv recordings do not have comskip tasks automatically created.  I consider this an advantage because I never want to comskip IR and live tv recordings and when you create a manual recording you'll be given the opportunity to assign tasks for it.  Some people may want comskip tasks created for all recordings; another example doc will discuss that option.</li></ul>

For this example, I'll be attaching the comskip tasks to a favourite I already have setup for The Office.  Go to <b><i>Setup > SJQ > Manage Favorites</i></b> and select any favourite you wish to setup comskip for.  Select Add beside SJQ Actions, as shown below.<br>
<br>
<img src='http://sagetv-addons.googlecode.com/svn/trunk/sjq4/media/images/wiki_comksip1_config_fav.jpg' />

Next, select SDCOMSKIP and HDCOMSKIP as the tasks to run when this favourite records.  You'll notice that I've chosen to queue the tasks when the recording starts.  You could also choose to wait and queue the tasks until the recording has stopped.  The choice is yours.<br>
<br>
<img src='http://sagetv-addons.googlecode.com/svn/trunk/sjq4/media/images/wiki_comskip1_add_to_favs.jpg' />

<h1>Configure Pretest</h1>

When you created the tasks you defined a pretest and copied the <code>comskip_test.groovy</code> file.  Open that file now.  Ignore everything in the file except for the block shown below.<br>
<br>
<pre><code>/*************** CONFIGURE VALUES IN THIS BLOCK ONLY! ***************/<br>
<br>
boolean checkRecordingType = true; // Check the recording is of the type specified on the command line (i.e. only process mpg recordings)<br>
boolean checkForEdl = true;        // If true, mark the task as skipped if an edl already exists<br>
String edlType = "edl";            // The edl extension (i.e. some people use .txt comskip files instead of .edl files)<br>
boolean comskipLive = false;       // If false, this test will push the task back to the queue while the recording is in progress<br>
<br>
/********************************************************************/<br>
</code></pre>

You can configure the pretest by changing the values in this block.  The comments explain what each option controls.  For <code>boolean</code> options you can set them to <code>true</code> or <code>false</code> (case sensitive).  The <code>checkForEdl</code> option will skip the task if an edl (or equivalent) already exists for the recording.  The <code>comskipLive</code> option will delay the start of comskip until the recording completes.<br>
<br>
<h1>All Done!</h1>

Hopefully, your comskip tasks are being queued.  If not then go over this example again and ask questions in the SJQv4 user forum as necessary.