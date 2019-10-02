# Quick Migration Steps

These instructions are for the ecosystem team as they implement parallel plugin support for the new embedding.

1. Create a new `[MyPlugin].java` file at `dev.flutter.plugins.[myplugin]`.

2. Have `[MyPlugin]` implement `FlutterPlugin`.

3. (Optional) If your plugin needs an `Activity` reference, also implement `ActivityAware`.

4. (Optional) If your plugin is expected to be held in a background `Service` at any point in time, implement `ServiceAware`.

5. Implement each plugin method as desired. Set up references as you attach to things. Clean up references as you detach from things.

6. Consider separating the `MethodChannelHandler` from the old version of `[MyPlugin]` and then use that `MethodChannelHandler` in both `io.flutter.plugins.[myplugin].[MyPlugin].java` and `dev.flutter.plugins.[myplugin].[MyPlugin].java`.

7. Add a static `registerWith()` method to your new `[MyPlugin]` class and have it call through to the `registerWith()` method in your old `[MyPlugin]` class.

8. (Optional but Recommended) Add unit tests to validate the serialization/deserialization of all incoming/outgoing channel messages.

# Basic Plugin

To get started with a Flutter Android plugin in code, start by implementing `FlutterPlugin`.

```java
public class MyPlugin implements FlutterPlugin {
  @Override
  public void onAttachedToEngine(@NonNull FlutterPluginBinding binding) {
    // TODO: your plugin is now attached to a Flutter experience.
  }

  @Override
  public void onDetachedFromEngine(@NonNull FlutterPluginBinding binding) {
    // TODO: your plugin is no longer attached to a Flutter experience.
  }
}
``` 

As shown above, your plugin may or may not be associated with a given Flutter experience at any given moment in time. You should take care to initialize your plugin's behavior in `onAttachedToEngine()`, and then cleanup your plugin's references in `onDetachedFromEngine()`.

The `FlutterPluginBinding` provides your plugin with a few important references:
* `binding.getFlutterEngine()`: Returns the `FlutterEngine` that your plugin is attached to, providing access to components like the `DartExecutor`, `FlutterRenderer`, and more.
* `binding.getApplicationContext()`: Returns the Android application's `Context` for the running app.
* `binding.getLifecycle()`: Returns a reference that can be used to obtain a `Lifecycle` object. If you need to use this lifecycle reference then you need add a project dependency on Flutter's Android lifecycle package.

# UI/Activity Plugin

If your plugin needs to interact with the UI, such as requesting permissions, or altering Android UI chrome, then you need to take additional steps to define your plugin. You must implement the `ActivityAware` interface.

```java
public class MyPlugin implements FlutterPlugin, ActivityAware {
  //...normal plugin behavior is hidden...

  @Override
  public void onAttachedToActivity(ActivityPluginBinding activityPluginBinding) {
    // TODO: your plugin is now attached to an Activity
  }

  @Override
  public void onDetachedFromActivityForConfigChanges() {
    // TODO: the Activity your plugin was attached to was destroyed to change configuration.
    // This call will be followed by onReattachedToActivityForConfigChanges().
  }

  @Override
  public void onReattachedToActivityForConfigChanges(ActivityPluginBinding activityPluginBinding) {
    // TODO: your plugin is now attached to a new Activity after a configuration change.
  }

  @Override
  public void onDetachedFromActivity() {
    // TODO: your plugin is no longer associated with an Activity. Clean up references.
  }
}
``` 

To interact with an `Activity`, your `ActivityAware` plugin must implement appropriate behavior at 4 stages. First, your plugin is attached to an `Activity`. You can access that `Activity` and a number of its callbacks through the provided `ActivityPluginBinding`.

Since `Activity`s can be destroyed during configuration changes, you must cleanup any references to the given `Activity` in `onDetachedFromActivityForConfigChanges()`, and then re-establish those references in `onReattachedToActivityForConfigChanges()`.

Finally, in `onDetachedFromActivity()` your plugin should clean up all references related to `Activity` behavior and return to a non-UI configuration.

# Service Plugin
TODO

# ContentProvider Plugin
TODO