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
    Intent defaultFlutter = new FlutterActivity.IntentBuilder().build(currentActivity);
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

## Subclassing FlutterActivity

It is not expected that `FlutterActivity` should require any subclassing to alter Flutter behavior. `FlutterActivity` is only responsible for minor setup responsibilities, as well as forwarding certain `Activity` callbacks to the underlying `FlutterFragment`.

If you discover a reason why you need to subclass `FlutterActivity` to alter Flutter behavior, please [file an issue](https://github.com/flutter/flutter/issues/new) so that the framework team can consider adding that behavior to `FlutterActivity`.