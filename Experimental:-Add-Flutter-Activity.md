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
        android:exported="true">
        <!-- This keeps the window background of the activity showing
             until Flutter renders its first frame. It can be removed if
             there is no splash screen (such as the default splash screen
             defined in @style/LaunchTheme). -->
        <meta-data
            android:name="io.flutter.app.android.SplashScreenUntilFirstFrame"
            android:value="true" 
            />
    </activity>
```

Notice the overridden `configChanges`. When Flutter is used as a standalone `Activity`, Flutter expects to handle all configuration changes, itself. Overriding configuration changes allows Flutter to update its UI without recreating the entire `FlutterView` within `FlutterActivity`.

### Launch FlutterActivity

Assuming that your Dart entrypoint is called `main()`, and you want to start your app at the default `/` route, all you have to do is launch `FlutterActivity` with an `Intent` and no extras.

```java
    startActivity(Intent(currentActivity, FlutterActivity.class));
```

To start your Flutter app at a route other than `/`, use the `FlutterActivity#EXTRA_INITIAL_ROUTE`.

```java
    startActivity(
        Intent(currentActivity, FlutterActivity.class)
            .putExtra(FlutterActivity.EXTRA_INITIAL_ROUTE, '/my/custom/route')
    );
```

To make Flutter transparent until the 1st frame is rendered (avoiding a black screen), use `FlutterActivity#EXTRA_SHOW_SPLASH_SCREEN`

```java
    startActivity(
        Intent(currentActivity, FlutterActivity.class)
            .putExtra(FlutterActivity.EXTRA_SHOW_SPLASH_SCREEN, true)
    );
```

## Subclassing FlutterActivity

It is not expected that `FlutterActivity` should require any subclassing to alter Flutter behavior. `FlutterActivity` is only responsible for minor setup responsibilities, as well as forwarding certain `Activity` callbacks to the underlying `FlutterFragment`.

If you discover a reason why you need to subclass `FlutterActivity` to alter Flutter behavior, please [file an issue](https://github.com/flutter/flutter/issues/new) so that the framework team can consider adding that behavior to `FlutterActivity`.