# Introduction #

In older versions of SJQ (up to and including v3.x), the SJQ task queue automatically kept track of what tasks had been completed on what objects and remembered these details forever on your behalf and ensured that the same task was never executed on the same object more than once (without the user explicitly requesting it).

In SJQv4, this is no longer the case.  The task queue is event based and does not keep track nor prevent tasks running on the same object more than once.  In SJQv4, the engine does not care and will run whatever tasks you insert into the queue on whatever objects you tell it to.

Sometimes, however, you need to know if a task has already been executed on an object because you're simulating the old SJQv3 behaviour of scanning all objects and queuing tasks and you don't want to queue a second instance of a task on an object while the first is still in the queue (waiting or running).

Luckily, there's an easy way to do this, but the burden is on the user to implement it when necessary.

# Details #

The key to doing this type of thing is to have a condition to check on in your test prescript (or scanner script) and only execute the task exe if that condition is met (or not met, as the case might be).

In the case of comskip, for example, your precheck is usually the existence of an `edl` file.  If the `edl` file exists, you've already ran comskip on the media file so you can skip running it again.  This is simple and obvious.  If you're using the simulated SJQv3 scanner approach, you simply check for an `edl` and if it exists you skip the media file and move on to the next one.

But what if there's no external resource to check against?  What if you're, for example, running a video editor that's applying your cut list and stripping the commercials from your recordings?  Let's say that there's no log file or some other file you can check for to determine if this process is running on the current media file.  How do you know not to queue up another task for this media object?

```
// Portion of scanner script
ServerClient sc = new ServerClient()
MediaFileAPI.GetMediaFiles('T').each {
   if(!MediaFileAPI.GetMediaFileMetadata(it, 'TASK_RUNNING') == '1') {
      sc.addTask('MYTASK', Factory.getMap(it))
      MediaFileAPI.SetMediaFileMetadata(it, 'TASK_RUNNING', '1')
   } else
      println "Already queued task for ${MediaFileAPI.GetMediaFileID(it)}; skipping!"
}
sc.close()
```

What am I doing here?  Before queuing my tasks, I check a metadata property on the object to see if I've already done this task on this object.  If I have then I skip the queuing.  If I haven't I queue the task in SJQ and, if successful, I set the metadata property to prevent future runs of my scanner from queuing another task for the same object.

And that's all there is to it.  The script for 'MYTASK' should remove the metadata value once it's successfully completed the task on the object.  Or, if you want to make sure MYTASK is only ever attempted once then do not remove that metadata value ever once you set it.

Happy scripting!