_**Everything in this doc and linked from this doc is experimental. These details WILL change. Do not use these instructions or APIs in production code because we will break you.**_

The new Android embedding introduces a new Java API for plugins. However, old plugins can still be used with the new Android embedding by using the provided shim.

The following example shows how to use the Android plugin shim to setup a standard `FlutterActivity`.

```java
public class FlutterShimPluginActivity extends FlutterActivity implements FlutterFragment.FlutterEngineProvider {

  @Override
  public FlutterEngine getFlutterEngine(@NonNull Context context) {
    // Create the FlutterEngine that will back this Activity.
    FlutterEngine flutterEngine = new FlutterEngine(context);

    // Create a ShimPluginRegistry and wrap the FlutterEngine with the shim.
    // TODO(mattcarroll): update this example with a real PlatformViewsController.
    ShimPluginRegistry shimPluginRegistry = new ShimPluginRegistry(flutterEngine, null);

    // Use the GeneratedPluginRegistrant to add every plugin that's in the pubspec.
    GeneratedPluginRegistrant.registerWith(shimPluginRegistry);

    // Return the configured FlutterEngine.
    return flutterEngine;
  }
}
```