_**Everything in this doc and linked from this doc is experimental. These details WILL change. Do not use these instructions or APIs in production code because we will break you.**_

# Add a Flutter View

Flutter can be added to an Android app as a single `View` in an `Activity`'s `View` hierarchy.

Before adding Flutter as a single `View`, you should consider if it is possible to add Flutter as a `Fragment` or an `Activity` to reduce your development burden. 

* [How to use a `FlutterActivity`](https://github.com/flutter/flutter/wiki/Experimental:-Add-Flutter-Activity)
* [How to use a `FlutterFragment`](https://github.com/flutter/flutter/wiki/Experimental:-Add-Flutter-Fragment)

If you really need to add Flutter as a single `View` then do the following.

## How to use FlutterView

### Create a FlutterView and add to layout

```java
  flutterView = new FlutterView(context);

  // We assume that you have some kind of container in your layout to hold flutterView
  container.addView(flutterView, /* desired LayoutParams */);
```

Creating a `FlutterView` does not automatically create a Dart execution context or setup any method channels. Therefore, after creating a `FlutterView`, a `FlutterEngine` must be configured and provided to the `FlutterView`.

### Create and configure a FlutterEngine

The `FlutterEngine` Java object represents your executing Flutter app, including Dart execution, platform channel registration, and plugin registration. A `FlutterView` is responsible for displaying pixels that come from a given `FlutterEngine`. `FlutterView` and `FlutterEngine` can vary independently - a single `FlutterView` can switch between different `FlutterEngine`s, and a single `FlutterEngine` can be attached to, and detached from, multiple `FlutterView`s.

A `FlutterEngine` represents a single app execution.  If you want to execute 3 different Flutter entrypoints, either concurrently or serially, then you will eventually create 3 `FlutterEngine` instances. This Wiki page only illustrates the creation of a single `FlutterEngine` and associated `FlutterView`

```java
  // Instantiate a new FlutterEngine.
  flutterEngine = new FlutterEngine(context, resources, false);

  // Connect this FlutterEngine's plugin registry to the Activity that owns the associated FlutterView.
  // This connection is made because some plugins require an Activity for correct operation.
  flutterEngine.getPluginRegistry().attach(activity);
```

### Create and configure platform plugin

Fundamental communication between the Android platform and your Flutter app takes place over a `MethodChannel` with the name `"flutter/platform"`. For example, Android's `onPostResume()` call must be forwarded over the `flutterPlatformChannel` with the message `"AppLifecycleState.resumed"`.

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

In addition to the "platform plugin", a few other method channels are required for sending important Android events.

The lifecycle channel is responsible for communicating Android lifecycle events to Flutter (except `onPostResume()` which is covered by the platform plugin).

The system channel is responsible for sending memory pressure warnings from Android to Flutter.

The navigation channel is responsible for sending initial route, push route, and pop route requests to Flutter. Developers should ensure that Android's hardware back button sends a `"popRoute"` message to Flutter, unless a developer has a specific reason to prevent back button behavior in his/her Flutter app.

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

A `FlutterView` does not know anything about a given Flutter application until a `FlutterEngine` is "attached" to the `FlutterView`. To attach a `FlutterEngine` to a `FlutterView`, pass the engine's `FlutterRenderer` and `DartExecutor` to the `FlutterView`.

```java
  flutterView.attachToFlutterRenderer(
    flutterEngine.getRenderer(),
    flutterEngine.getDartExecutor()
  );
```

### Start running your Flutter/Dart app

To start executing a Flutter app, invoke `runFromBundle()` on a `FlutterEngine`'s `DartExecutor`. `FlutterRunArguments` need to be configured as desired before invoking `runFromBundle()`.

An initial route can optionally be configured before execution begins.

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

Flutter requires many signals from the host operating system. When using a `FlutterActivity` or `FlutterFragment`, all or most of these signals are forwarded on the developer's behalf. When using a `FlutterView`, developers must carefully forward each relevant call manually.

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

By the time a developer has implemented all above responsibilities, he/she has essentially re-implemented `FlutterFragment`. Therefore, for maintenance purposes, developers should only directly use a `FlutterView` when there is a good reason not to use a `FlutterFragment`.