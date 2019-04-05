_**Everything in this doc and linked from this doc is experimental. These details WILL change. Do not use these instructions or APIs in production code because we will break you.**_

# Add a Flutter Activity

`FlutterActivity` can be used directly to launch a Flutter app, or `FlutterActivity` can be subclassed for desired customizations.

## Using FlutterActivity Directly

### Declare Activity in AndroidManifest.xml

As with any other `Activity`, start by declaring the `Activity` in `AndroidManifest.xml`.

```xml
    <activity
        android:name="io.flutter.embedding.android.FlutterActivity"
        android:theme="@style/LaunchTheme"
        android:configChanges="orientation|keyboardHidden|keyboard|screenSize|locale|layoutDirection|fontScale|screenLayout|density"
        android:hardwareAccelerated="true"
        android:windowSoftInputMode="adjustResize"
        android:exported="true"
        />
```

Notice the overridden `configChanges`. When Flutter is used as a standalone `Activity`, Flutter expects to handle all configuration changes, itself. Overriding configuration changes allows Flutter to update its UI without recreating the entire `FlutterView` within `FlutterActivity`.

### Launch FlutterActivity

Assuming that your Dart entrypoint is called `main()`, and you want to start your app at the initial route of `/`, all you have to do is retrieve an `Intent` from `FlutterActivity` as follows.

```java
    // Launches FlutterActivity, runs a Dart method called 'main()', and displays an initial
    // route in Flutter of '/'.
    Intent defaultFlutter = new FlutterActivity.createDefaultIntent(currentActivity);
    startActivity(defaultFlutter);
```

To customize the initial Dart method that is run and/or the initial Flutter route, use `FlutterActivity`'s `IntentBuilder` to customize your use-case.

```java
    // Launches FlutterActivity, runs a Dart method called 'someOtherMethod()', and displays an initial
    // route in Flutter of 'someOtherRoute'.
    Intent customFlutter = new FlutterActivity.IntentBuilder()
      .dartEntrypoint("someOtherMethod")
      .initialRoute("someOtherRoute")
      .build(currentActivity);
    startActivity(customFlutter);
```

## Using a cached FlutterEngine

By default, a `FlutterActivity` will create a new `FlutterEngine` when it is created. Creating a new `FlutterEngine` comes with a warm-up period that results in a blank screen for a couple seconds before Flutter is able to render its first frame.

To avoid the initial visual delay of creating a new `FlutterEngine`, you can create a `FlutterEngine` instance before you need it and then use it within your `FlutterActivity`. To use an existing `FlutterEngine` in your `FlutterActivity` you will need to subclass `FlutterActivity` as shown below.

```java
public class MyFlutterActivity extends FlutterActivity implements FlutterFragment.FlutterEngineProvider {
  // This is the method that others will use to create
  // an Intent that launches MyFlutterActivity.
  public static Intent createDefaultIntent(@NonNull Context launchingContext) {
    return new IntentBuilder().build(launchingContext);
  }

  // You need to define an IntentBuilder subclass so that the
  // IntentBuilder uses MyFlutterActivity instead of a regular FlutterActivity.
  private static class IntentBuilder extends FlutterActivity.IntentBuilder {
    // Override the constructor to specify your class.
    IntentBuilder() {
      super(MyFlutterActivity.class);
    }
  }

  // This is the method where you provide your existing FlutterEngine instance.
  @Nullable
  @Override
  public FlutterEngine getFlutterEngine(@NonNull Context context) {
    FlutterEngine flutterEngine;

    // You might hold your engine in a static variable.
    flutterEngine = MyFlutterEngineCache.getFlutterEngine();

    // or you might hold your engine in your application class.
    flutterEngine = ((MyApp) getApplication()).getFlutterEngine();

    return flutterEngine;
  }
}
```

Warming up a `FlutterEngine` requires that you instantiate the `FlutterEngine` and begin executing Dart code. Here is what it might look like to warm up an engine when your application starts. It is not recommended that you actually warm up a `FlutterEngine` in your `Application`'s `onCreate()` method because it will cause an app-launch lag.

```java
class MyApp extends Application {
  private FlutterEngine flutterEngine;

  @Override
  public void onCreate() {
    super.onCreate();

    // Flutter must be initialized before FlutterEngines can be created.    
    FlutterMain.startInitialization(this);
    FlutterMain.ensureInitializationComplete(this, new String[]{});

    // Instantiate a FlutterEngine.
    flutterEngine = new FlutterEngine(this);

    // Start running Dart code. This means that you need to select a Dart
    // entrypoint, and an initial route when you warm up your engine.
    DartExecutor.DartEntrypoint entrypoint = new DartExecutor.DartEntrypoint(
      getResources().getAssets(),
      FlutterMain.findAppBundlePath(this),
      "main"
    );
    flutterEngine.getDartExecutor().executeDartEntrypoint(entrypoint);
  }
}
```