_**Everything in this doc and linked from this doc is experimental. These details WILL change. Do not use these instructions or APIs in production code because we will break you.**_

# Add a Flutter View

Many Android apps cannot use `FlutterActivity`s because they navigate to multiple screens within a single `Activity`. Additionally, some apps prefer not to use `Fragment`s, which precludes the use of `FlutterFragment`. In a situation where only a `View` will do, Flutter offers `FlutterView`.

`FlutterView` requires a lot more call-forwarding than a `FlutterFragment` or `FlutterActivity`, so if you can use a `FlutterFragment` or `FlutterActivity`, you should consider using one of those instead so that you have less code to maintain.

* [How to use a `FlutterActivity`](https://github.com/flutter/flutter/wiki/Experimental:-Add-Flutter-Activity)
* [How to use a `FlutterFragment`](https://github.com/flutter/flutter/wiki/Experimental:-Add-Flutter-Fragment)

## How to use FlutterView

### Create a FlutterView and add to layout

```java
  flutterView = new FlutterView(context);

  // We assume that you have some kind of container in your layout to hold flutterView
  container.addView(flutterView, /* desired LayoutParams */);
```

Creating a `FlutterView` does not automatically create a Dart execution context or setup any method channels. Therefore, after creating a `FlutterView`, a `FlutterEngine` must be configured and provided to the `FlutterView`.

### Create and configure a FlutterEngine

```java
  // Instantiate a new FlutterEngine.
  flutterEngine = new FlutterEngine(context, resources, false);

  // Connect this FlutterEngine's plugin registry to the Activity that owns the associated FlutterView.
  // This connection is made because some plugins require an Activity for correct operation.
  flutterEngine.getPluginRegistry().attach(activity);
```

### Create and configure platform plugin

```java
  platformPlugin = new PlatformPlugin(activity);
  MethodChannel flutterPlatformChannel = new MethodChannel(
    flutterEngine.getDartExecutor(),
    "flutter/platform",
    JSONMethodCodec.INSTANCE
  );
  flutterPlatformChannel.setMethodCallHandler(platformPlugin);
```

### Create and configure fundamental channels

```java
  flutterLifecycleChannel = new BasicMessageChannel<>(
    flutterEngine.getDartExecutor(),
    "flutter/lifecycle",
    StringCodec.INSTANCE
  );

  flutterSystemChannel = new BasicMessageChannel<>(
    flutterEngine.getDartExecutor(),
    "flutter/system",
    JSONMessageCodec.INSTANCE
  );

  flutterNavigationChannel = new BasicMessageChannel<>(
    flutterEngine.getDartExecutor(),
    "flutter/navigation",
    JSONMessageCodec.INSTANCE
  );
```

### Attach the FlutterEngine to the FlutterView

```java
  flutterView.attachToFlutterRenderer(
    flutterEngine.getRenderer(),
    flutterEngine.getDartExecutor()
  );
```

### Start running your Flutter/Dart app

```java
  if (flutterEngine.getDartExecutor().isApplicationRunning()) {
    // A DartExecutor cannot be restarted. You will need to decide what to do if someone tries to
    // execute a new Dart entrypoint with your FlutterEngine.
  }

  // If you want to use a non-default "/" initial route then set the initial route.
  // Setting the initial route requires the flutterNavigationChannel created earlier.
  flutterNavigationChannel.invokeMethod("setInitialRoute", initialRoute);

  // Build args for Dart execution.
  FlutterRunArguments args = new FlutterRunArguments();
  args.bundlePath = FlutterMain.findAppBundlePath(applicationContext());
  args.entrypoint = "main";
  args.defaultPath = null;
  flutterEngine.getDartExecutor().runFromBundle(args);

  flutterView.resetAccessibilityTree();
```

### Implement needed Android callbacks

```java
  @Override
  public void onStart() {
    super.onStart();
    
    flutterLifecycleChannel.send("AppLifecycleState.inactive");
  }

  @Override
  public void onPostResume() {
    super.onPostResume();
    
    flutterView.updateAccessibilityFeatures();
    platformPlugin.onPostResume();
    flutterLifecycleChannel.send("AppLifecycleState.resumed");
  }

  @Override
  public void onPause() {
    super.onPause();
  
    flutterLifecycleChannel.send("AppLifecycleState.inactive");
  }

  @Override
  public void onStop() {
    super.onStop();

    flutterLifecycleChannel.send("AppLifecycleState.paused");
  }

  @Override
  public void onDestroy() {
    super.onDestroy();

    // When an Activity is destroyed, you can either destroy the FlutterEngine, or you can
    // simply detach your FlutterEngine from the FlutterView and keep it for later use.
    flutterEngine.getPluginRegistry().onViewDestroy(flutterEngine);
    flutterView.detachFromFlutterRenderer();
    if (wantToDestroyEngine) {
      flutterEngine.destroy();
    }
  }

  @Override
  public void onBackPressed() {
    super.onBackPressed();
    
    flutterNavigationChannel.invokeMethod("popRoute", null);
  }

  @Override
  public void onRequestPermissionsResult(int requestCode, @NonNull String[] permissions, @NonNull int[] grantResults) {
    flutterEngine.getPluginRegistry().onRequestPermissionsResult(requestCode, permissions, grantResults);
  }

  @Override
  public void onNewIntent(@NonNull Intent intent) {
    flutterEngine.getPluginRegistry().onNewIntent(intent);
  }

  @Override
  public void onActivityResult(int requestCode, int resultCode, Intent data) {
    flutterEngine.getPluginRegistry().onActivityResult(requestCode, resultCode, data);
  }

  @Override
  public void onUserLeaveHint() {
    flutterEngine.getPluginRegistry().onUserLeaveHint();
  }

  @Override
  public void onTrimMemory(int level) {
    super.onTrimMemory(level);

    if (level == TRIM_MEMORY_RUNNING_LOW) {
      sendMemoryPressureWarningToFlutter();
    }
  }

  @Override
  public void onLowMemory() {
    super.onLowMemory();

    sendMemoryPressureWarningToFlutter();
  }

  private void sendMemoryPressureWarningToFlutter() {
    Map<String, Object> message = new HashMap<>(1);
    message.put("type", "memoryPressure");
    flutterSystemChannel.send(message);
  }
```

**TODO(mattcarroll):** fill in rest of this document