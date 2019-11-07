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

To customize the initial Flutter route, use `FlutterActivity`'s `IntentBuilder` to customize your use-case.

```java
    // Launches FlutterActivity and displays an initial route in Flutter of 'someOtherRoute'.
    Intent customFlutter = new FlutterActivity.IntentBuilder()
      .initialRoute("someOtherRoute")
      .build(currentActivity);
    startActivity(customFlutter);
```

_**Note: there was a version of the new Android embedding that supported choosing the Dart entrypoint via `Intent`, however it was later determined that exposing the choice of Dart entrypoint from an `Activity` `Intent` introduced a security vulnerability whereby other apps could launch your `FlutterActivity` and select an entrypoint that you wouldn't want it to run. For that reason, `FlutterActivity` no longer supports customization of the Dart entrypoint via `Intent`. To select a different Dart entrypoint, you can either use `meta-data` in your `Activity`'s manifest definition, or you can subclass `FlutterActivity` and override the method that returns the Dart entrypoint.**_

### Select Dart entrypoint via manifest

The Dart entrypoint executed by `FlutterActivity` can be configured via `meta-data` in `AndroidManifest.xml`. The same configuration works for subclasses, too.

```xml
<application ...>
  <activity
    android:name="io.flutter.embedding.android.FlutterActivity"
    ...
    >
    <meta-data
      android:name="io.flutter.Entrypoint"
      android:value="myMainDartMethod"
      />
  </activity>
</application>
```

## Using a cached FlutterEngine

By default, a `FlutterActivity` will create a new `FlutterEngine` when it is created. Creating a new `FlutterEngine` comes with a warm-up period that results in a blank screen for a brief period before Flutter is able to render its first frame.

To avoid the initial visual delay of creating a new `FlutterEngine`, you can create a `FlutterEngine` instance before you need it and then use it within your `FlutterActivity`. You have two options for using a cached `FlutterEngine`. First, you can use Flutter's `FlutterEngineCache` to cache a `FlutterEngine`, or you can manually cache a `FlutterEngine` wherever you'd like and then subclass `FlutterActivity` and override the appropriate method to utilize your cached `FlutterEngine`. These options are outlined below.

### Use FlutterEngineCache

To use Flutter's standard `FlutterEngineCache`, start by instantiating and pre-warming your `FlutterEngine`.

```java
// Instantiate your FlutterEngine.
FlutterEngine flutterEngine = new FlutterEngine(context);

// Pre-warm your FlutterEngine by starting Dart execution.
flutterEngine
  .getDartExecutor()
  .executeDartEntrypoint(
    DartEntrypoint.createDefault()
  );
```

With a pre-warmed `FlutterEngine`, place the `FlutterEngine` in `FlutterEngineCache` using an ID of your choice.

```java
FlutterEngineCache
  .getInstance()
  .put("my_engine_id", flutterEngine);
```

Your `FlutterEngine` is now pre-warmed, cached, and ready to use with a `FlutterActivity`. To utilize your cached engine in a `FlutterActivity`, launch `FlutterActivity` as follows:

```java
Intent intent = FlutterActivity
  .withCachedEngine("my_engine_id")
  .build(context);

startActivity(intent);
```

### Subclass FlutterActivity

To use an existing `FlutterEngine` in your `FlutterActivity` you can subclass `FlutterActivity`. But first you must instantiate and pre-warm your `FlutterEngine`:

```java
// Instantiate your FlutterEngine.
FlutterEngine flutterEngine = new FlutterEngine(context);

// Pre-warm your FlutterEngine by starting Dart execution.
flutterEngine
  .getDartExecutor()
  .executeDartEntrypoint(
    DartEntrypoint.createDefault()
  );
```

Then, you might choose to cache your `flutterEngine` in a static member, or your `Application` class, or anywhere else that you can access from your `FlutterActivity` subclass:

```java
// Using your own static cache.
MyFlutterEngineCache.setFlutterEngine(flutterEngine);

// Using a reference to your application subclass.
myApplication.setFlutterEngine(flutterEngine);
```

Finally, use your cached `FlutterEngine` in your `FlutterActivity` subclass.

```java
public class MyFlutterActivity extends FlutterActivity {
  // This is the method where you provide your existing FlutterEngine instance.
  @Nullable
  @Override
  public FlutterEngine provideFlutterEngine(@NonNull Context context) {
    FlutterEngine flutterEngine;

    // You might hold your engine in a static variable.
    flutterEngine = MyFlutterEngineCache.getFlutterEngine();

    // or you might hold your engine in your application class.
    flutterEngine = ((MyApp) getApplication()).getFlutterEngine();

    return flutterEngine;
  }
}
```

## FlutterActivity with a transparent background

To display a `FlutterActivity` with a transparent background, you must apply a theme with the appropriate styles, and launch the `FlutterActivity` with the correct configuration.

First, ensure that the theme that is applied to your `FlutterActivity` contains the following style attribute:

```xml
<style name="MyTheme" parent="@style/MyParentTheme">
  <item name="android:windowIsTranslucent">true</item>
</style>
```

Second, when you launch your `FlutterActivity`, configure it to have a transparent background as shown below:

```java
// If you're not using a cached FlutterEngine:
startActivity(
  FlutterActivity
    .withNewEngine()
    .backgroundMode(FlutterActivity.BackgroundMode.transparent)
    .build(context)
);

// If you are using a cached FlutterEngine.
startActivity(
  FlutterActivity
    .withCachedEngine("my_engine_id")
    .backgroundMode(FlutterActivity.BackgroundMode.transparent)
    .build(context)
);
```

With the above changes, if your Flutter UI has any transparent areas, those areas will show through to the `Activity` beneath your `FlutterActivity`.