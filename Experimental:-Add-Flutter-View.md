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

**TODO(mattcarroll):** fill in rest of this document