

# Introduction #

SJQv4 introduces a new way to queue up tasks to be executed.  Gone is the periodic scanning of SageTV objects, filtering them against the server ruleset and then loading matches into the queue.  Instead, SJQv4 listens for SageTV 7.0 core events and acts upon them.

# Supported Event Listeners #

The SJQv4 server will listen for and respond to the following core events:

## RecordingStarted/RecordingSegmentAdded ##

Anytime SJQ receives a `RecordingStarted` or a `RecordingSegmentAdded` event it will do the following:

  * If the `MediaFile` attached to the event is not a tv recording then ignore the event
  * If the recording is a manual recording then check the `SJQ4_TASKS` property via `AiringAPI.GetManualRecordProperty()`; retrieve the configured list of tasks to queue for this manual recording.
  * If the recording (also) matches a favourite then check the `SJQ4_TASKS` property via the `FavoriteAPI.GetFavoriteProperty()` call.  Retrieve the configured task list for the assoicated favourite of this recording.
  * Generate a set of tasks consisting of the union of the two above lists.
  * For each task in the set, add the task to the queue using the `MediaFile` from the event as the source of metadata for the task insertion.

## MediaFileImported ##

Anytime SJQ receives a `MediaFileImported` event it will do the following:

  * If the `MediaFile` attached to the event is a tv recording then ignore the event
  * Grab the task list defined for this event via `DataStore.get().getSetting(ImportedMediaTaskLoader.TASK_PROP, "")`
  * For each task in the list, add a task to the queue using the `MediaFile` from the event as the source of metadata for the task insertion.

## SystemMessagePosted ##

Anytime SJQ receives a `SystemMessagePosted` event it will do the following:

  * Grab the task list defined for this event via `DataStore.get().getSetting(SystemMessageTaskLoader.TASK_PROP, "")`
  * For each task in the list, add a task to the queue using the `SystemMessage` from the event as the source of metadata for the task insertion.

# Reading and Manipulating Task Lists #

SJQ provides various utility methods for reading and manipulating the task lists.  Basically, you should always read the string from the `DataStore` or from the Sage object properties, as appropriate, and then use the [com.google.code.sagetvaddons.sjq.utils.TaskList](http://sagetv-addons.googlecode.com/svn/trunk/sjq4/doc/com/google/code/sagetvaddons/sjq/utils/TaskList.html) class for manipulating the task list.  If you make changes and need to save them then just use the returned string from the methods in this class.  There is no need to manually split the string or try to directly add/edit the string of task ids, etc.  The `TaskList` class should be able to perform all required operations, if not then ask for the ops that are missing.

# Other Event Listeners Can Be Added #

Other event listeners can be added as demand for them arises.  For now, however, I think these three are going to cover the majority of users' needs.