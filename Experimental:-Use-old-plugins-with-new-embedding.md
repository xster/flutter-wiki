_**Everything in this doc and linked from this doc is experimental. These details WILL change. Do not use these instructions or APIs in production code because we will break you.**_

The new Android embedding introduces a new Java API for plugins. However, old plugins can still be used with the new Android embedding by using the provided shim.

The following example shows how to use the Android plugin shim to setup a standard `FlutterActivity`.

```java
public class FlutterShimPluginActivity extends FlutterActivity {

  @Override
  public void configureFlutterEngine(@NonNull FlutterEngine flutterEngine) {
    // Create a ShimPluginRegistry and wrap the FlutterEngine with the shim.
    ShimPluginRegistry shimPluginRegistry = new ShimPluginRegistry(
      flutterEngine, 
      new PlatformViewsController()
    );

    // Use the GeneratedPluginRegistrant to add every plugin that's in the pubspec.
    GeneratedPluginRegistrant.registerWith(shimPluginRegistry);
  }
}
```