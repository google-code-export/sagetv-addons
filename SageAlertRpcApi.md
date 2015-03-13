# SageAlert RPC (Client) API #

SageAlert provides a client API that allows developers to integrate SageAlert support into their own applications.  By using the client API, applications can trigger alerts within a SageAlert server to notify users of significant events from their own apps.

Integrating SageAlert into your application couldn't be easier.  Simply download the latest RPC client zip file from the project [downloads](http://code.google.com/p/sagetv-addons/downloads/list) section and include all the jar files from the zip into your application's classpath.  Once you do that then triggering an alert is as simple as instantiating a `RemoteEventLauncher` object and calling the appropriate methods to trigger events.  The client API allows developers to trigger three types of events in a SageAlert system:

  * Trigger an informational level alert by calling the `fireInfo()` method
  * Trigger a warning level alert by calling the `fireWarning()` method
  * Trigger an error level alert by calling the `fireError()` method

Users can then configure the various alert types within their SageAlert installations to receive notifications as so desired.

The following Java application (copied from the javadocs) shows how to trigger an alert in a remote application:

```
    package sagealert.test;
    
    import com.google.code.sagetvaddons.sagealert.rpc.*;
    import java.net.URL;
    import java.net.MalformedURLException;
    
    public final class TestClient {
       static public void main(String[] args) {
          try {
             RemoteEventLauncher clnt = new RemoteEventLauncher(new URL("http://192.168.0.1/sagealert"), "my_jetty_id", "my_jetty_pwd");
             clnt.fireInfo("My first remote alert!", "This is the text of my first remote alert being sent to SageAlert.");
             System.out.println("Remote event fired successfully!");
          } catch(ApiVersionException e) {   // Thrown by the constructor if the client API version != the server API version
             e.printStackTrace();
          } catch(RpcException e) {          // Thrown by the call to fireInfo() if the RPC call fails
             e.printStackTrace();
          } catch(MalformedURLException e) { // Throw by the constructor if the given SageAlert URL is invalid
             e.printStackTrace();
          } catch(Exception e) {             // Catch any other error that may occur during program execution
             e.printStackTrace();
          }
       }
    }
```

Complete javadocs for the client API are available from the project [downloads](http://code.google.com/p/sagetv-addons/downloads/list) section.