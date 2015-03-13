For now, just a link.  I'll eventually move the content to this wiki...

[Click here](http://forums.sagetv.com/forums/showthread.php?p=476647&postcount=340)

# Upgrade Instructions #

If you're upgrading SageGroovy simply by overwriting your existing installation with the latest zip file then you **MUST** check the lib folder after upgrade to ensure there are no duplicate jar files.

For example, the 4.0.0.1482.0 version upgraded Groovy to v1.7.10 from v1.7.5.  So if you're upgrading then you will have two groovy jars in your lib folder (groovy-all-1.7.5.jar and groovy-all-1.7.10.jar).  Remove the 1.7.5 jar before starting up SageGroovy for the first time.  **Failure to do so will result in runtime problems!**