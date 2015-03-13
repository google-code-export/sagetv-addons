

# Introduction #

This doc discusses the various high level components of SJQv4 along with some typical use cases.

# The Server Plugin #

The server plugin is installed via the Sage plugin manager and provides the central engine that controls who does what as far as task assignment goes.  The server maintains all state information about the SJQ installation, maintains the active task queue, maintains state about all registered task clients, and ultimately decides which task clients are assigned which tasks.  The server contains four subsystems, each discussed below.

## The Timer Thread ##

The timer thread in the server is created when the plugin is started by the core and stopped when the plugin is disabled/uninstalled.  The timer then registers all of the various periodic tasks that the server is responsible for running.

### AgentManager TimerTask ###

The AgenetManager task attempts to connect to each registered task client and issues the `PING` command on each one.  When a response is received, the manager puts the client in `ONLINE` state and updates the client's details with the response received from the task client.  If a task client does not respond or a connection cannot be established then the client is put in `OFFLINE` state and no other details are updated.  Clients in `OFFLINE` state cannot be assigned tasks.  During development, this task is scheduled to run every two minutes and is not configurable.  In production, this option will be configurable and a default of 30 or 60 minutes would be likely.

### TaskQueue ###

The TaskQueue is triggered via a TimerTask.  When triggered, the TaskQueue gets a list of all tasks in `WAITING` or `RETURNED` state and attempts to assign them to an `ONLINE` task client for execution.  The TaskQueue is responsible for checking that a potential task client for a given task is:

  * In `ONLINE` state
  * Is enabled (by checking its client schedule setting)
  * Has enough free resources to run the task
  * Is not already running `MAX_INSTANCES` instances of this task type
  * Is enabled for this specific task type (by checking its task schedule setting)

When a potential task client passes all of these checks it will then send the task to the client.

## Cron Thread ##

The cron scheduler is created when the plugin is started and halted when the plugin is stopped.  The cron scheduler is configured to read the `SageTV/plugins/sjq/crontab` file for its scheduling directions.  The crontab file is basically a standard `*`nix crontab file except instead of executing programs, it's used to add tasks to the SJQv4 queue.  The Sjq4Crontab wiki doc discusses more details about the format of the `crontab` and the behaviour of the cron subsystem in SJQv4.

## Sage Event Listeners ##

The event listener listens for Sage events and will add tasks to the queue based on the events it receives.  I'm still working out the exact implementation details for this subsystem.  The Sjq4EventListeners wiki doc will explain the final details of how this subsystem will work.

## Server Agent ##

The server agent is a socket listener that is started when the plugin starts and stopped when the plugin is stopped.  The server agent listens for socket connections from either task clients or a `ServerClient`.  These clients can then perform various commands that do things such as update task info, update task log output, add tasks to the task queue, etc.  Any entity wishing to read/write from the SJQ server that is not running in the same JVM as the SJQ server **MUST** communicate via the server agent.  Only the server agent guarantees synchronization across both threads and processes.  Task clients, STVIs, web interfaces, etc. all must communicate with the SJQ server via the server agent.

# The Task Client #

The task client is a separate, standalone application that is configured to receive and perform tasks queued up in the SJQ task queue.  A task client is configured to perform one or more tasks and waits for the server to assign it tasks.  At the highest level, the task client consists of three subsystems.

## The Agent (Listener) ##

When started, the task client will create a socket listener on the configured port.  The port number must be registered with the server so that the server knows how to contact each task client.  This socket listener waits for commands from the server and responds to them.  The two major commands a task client responds to are `PING` and `EXE`.

## The Pretest Environment ##

Tasks can optionally perform a "pretest" before starting a task execution.  This pretest can be used to determine if it's "safe" to perform a given task at assignment time.  "Safe" is subjective and can mean anything.  A typical safety check might to ensure that there are no clients connected to the SageTV server before starting a certain task.

A pretest can also decide that the assigned task doesn't actually need to be performed and can move the task to `SKIPPED` state.  A typical use case of this feature would be if a task client was assigned a comskip task then the pretest might check for the edl file for the recording and if it's found then it'll put the task in `SKIPPED` state and not run the comskip.exe on the recording.

All pretests must be a script capable of running in the SJQ scripting environment.  The official scripting language of SJQ is [Groovy](http://groovy.codehaus.org/), but users can add support for any other scripting languages supported by Java's JSR-223 scripting API.  During development I experimented with Groovy (Java-like), Sleep (Perl-like), Jython (Python), and JavaScript.  By far, Groovy was my favourite and is the one I've chosen to support.  However, users who prefer other languages are encouraged to use what they know.  Support for other languages exist beyond the ones I tested and the Sjq4ScriptingSupport wiki doc goes into much greater detail about the scripting environment in SJQv4.

## The Task Execution Environment ##

Finally, a task to be executed is run in the execution environment.  Basically, this is just a separate thread spawned that runs the configured exe or SJQ script.  The results of this execution determine the final state of the task as reported back to the SJQ server.

# How to Get a Task to Execute #

This section describes how to get a task to execute on a task client.  Each step is described in terms of how the code works, not necessarily how a typical user might accomplish the actions.  The idea here is to understand how the code interacts and once you have that, you should be able to envision how to create the UI elements for a user to perform the same set of actions.

## Register a Task Client ##

I assume a task client is already installed, configured and started.  Let's assume the task client is running on host `192.168.0.100` and was configured to run on port `23344`.

Registering a task client is relatively straightforward.  Create a new `Client` instance, grab a `DataStore` connection and save the client to the datastore.  You probably want to check that a client isn't already registered at the same host/port combination so as not to overwrite an already existing registration.  Mind you, not checking isn't catastrophic as the overwritten data would be restored the next time the server pings the task client.

```
import com.google.code.sagetvaddons.sjq.shared.Client;
import com.google.code.sagetvaddons.sjq.server.DataStore;

// These values would come from some UI component
String host = "192.168.0.100";
int port = 23334;

DataStore ds = DataStore.get();
Client clnt = ds.getClient(host, port);
if(clnt != null)
   System.err.println("ERROR: This client is already registered!");
else
   ds.saveClient(new Client(host, port));
```

The above code is obviously missing error checking, etc.

Now you have a task client registered with the server.

## Adding a Task to the Queue ##

Adding a task to the queue from the web or an STVi means going through a `ServerClient` connection.  In fact, you can _always_ use a `ServerClient` connection to add tasks to the queue and doing so means you're always going to be properly synchronized.  Eventually, the server code will be changed to use `ServerClient` as well.

```
import com.google.code.sagetvaddons.sjq.taskqueue.ServerClient;
import com.google.code.sagetvaddons.metadata.Factory;

// You received this input from the user somehow; this is the task type they want
// to add to the task queue
String taskId = "MYTASK";

// This is a reference to some Sage object they want to associate with the task;
// if they queued this task from a recording details page, for example, then this would
// be a reference to a MediaFile object.  Assume it's already been set somehow.
Object sageObj;

ServerClient clnt = new ServerClient();
long id = clnt.addTask(taskId, Factory.getMap(sageObj));
clnt.close();
System.out.println("Task " + id + " was added to the queue!");
```

Of interest in this code:

  * Always try to remember to `close()` your `ServerClient` connections.
  * `Factory.getMap()` accepts an object and returns a `Map<String, String>` of metadata about that object.  When passed a Sage object, it returns a map of environment variables about the object that are then inserted into the task's execution environment.  For a MediaFile, this map would contain things like the file name, the title, the MediaFile id, etc.

## Executing the Task ##

Your job is done! :)  You've now registered a task client and inserted a task into the queue.  Just make sure the task client you've registered is configured to accept tasks of type `MYTASK` and as long as it is, the server should assign the task to the task client.

## Other Interesting Things ##

The other interesting things all come from `ServerClient`.  You use `ServerClient` to read the active queue, get a list of registered task clients, etc.  Actually, that's all it does for now, but over the coming weeks additional features like deleting tasks from the queue, cancelling running tasks, etc. will all be added to `ServerClient`.  Hopefully, it becomes clear that everything you need to do is done through `DataStore` and `ServerClient`.