# Upgrading HttpClient #

The SD EPG plugin requires HttpClient v4.2.1. If you already have this plugin installed on your Sage system then it is likely at v4.0.x. Unfortunately, these older versions of the plugin were packaged incorrectly, making it impossible for them to be upgraded.

The only way to get this plugin upgraded to the required level is to uninstall it first. But to uninstall it, you must remove all plugins that depend on it first. :( I apologize in advance, but there's just no other way.

To remove this plugin, start by attempting to uninstall the HttpClient plugin. This will error out and tell you which plugin depends on it. Then go to that plugin and attempt to uninstall it. If that one fails, it'll tell you which one it depends on, then go try and remove that and so on. **You must do the same for the HttpCore plugin - both must be removed and upgraded.**

Keep track of all the plugins you had to uninstall then when you're finally able to uninstall HttpCore and HttpClient you can then go back and reinstall the plugins you had to remove. Start by reinstalling General plugins as those ones should automatically reinstall many of the library plugins you may have had to remove.