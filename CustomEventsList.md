# Development Ceased #

**13 Oct 2011**: Please note that development on this plugin has stopped.  There will be no further updates to this plugin.  The source code remains available in the svn repository for those who wish to modify it.

# Introduction #

This doc lists all of the events fired by the Custom Events plugin.  Feel free to suggest additional events by opening a feature request ticket.


# Events List #

The table below lists all of the events the current version of this plugin will fire.  The Arugments column lists the keys of the args map that is passed to the `postEvent()` Sage API call when an event is fired.  The type of each argument is shown in parenthesis.  The Since column specifies which version of Custom Events the given event was introduced in.

| **Since** | **Event ID** | **Arguments** | **Fired When...** |
|:----------|:-------------|:--------------|:------------------|
| 1.0.0.955 | `CustomEvents_LowRecordingSpace` | `[MinSpace (java.lang.Long), FreeSpace (java.lang.Long)]` | The available recording space falls below the configured minimum; defaults to 25GB, configure via plugin's configuration panel |