_If you `flutter create`d your project prior to version 1.12, this may apply to your project_

# Background

In order to better support the execution environments of adding Flutter to an existing project, the old Android platform-side wrappers hosting the Flutter runtime at [`io.flutter.app.FlutterActivity`](https://github.com/flutter/engine/blob/master/shell/platform/android/io/flutter/app/FlutterActivity.java) and their associated classes are now deprecated. New wrappers at [`io.flutter.embedding.android.FlutterActivity`](https://github.com/flutter/engine/blob/master/shell/platform/android/io/flutter/embedding/android/FlutterActivity.java) and associated classes now replace them. 

Those classes better support real world scenarios where the FlutterActivity isn't the first and only Android Activity in an application. 

# Motivation

Your existing full-Flutter projects aren't immediately affected and will continue to work as before for the foreseeable future. 

However, the new Android wrappers also introduce a new set of Android plugin development APIs. Plugins developed exclusively on the new plugins API will not work on older pre-1.12 Android projects. Building a pre-1.12 Android project that uses plugins created after 1.12 will yield a build-time error unless the plugin developer explicitly opted to create a second backward compatible implementation. 

# Full-Flutter app migration
 
_This guide assumes you haven't manually modified your Android host project for your Flutter project. If you did, consult the add-to-app migration guide below._ 

If you opt to migrate your standard `flutter create`d project, follow the following steps:

1. Delete `android/app/src/main/java/[your.package.name]/MainActivity.java`.
2. Open `android/app/src/main/AndroidManifest.xml`.
3. Remove the reference to `FlutterApplication` from the application tag.

Previous configuration:
```xml
<application
  name="io.flutter.FlutterApplication"
  >
  <!-- code omitted -->
</application>
```

New configuration:
```xml
<application
  >
  <!-- code omitted -->
</application>
```

4. Change the `MainActivity` reference to `FlutterActivity`.

Previous configuration:
```xml
<activity android:name=".MainActivity"
  android:launchMode="singleTop"
  android:theme="@android:style/Theme.Black.NoTitleBar"
 android:configChanges="orientation|keyboardHidden|keyboard|screenSize|locale|layoutDirection|fontScale|screenLayout|density|uiMode"
  android:hardwareAccelerated="true"
  android:windowSoftInputMode="adjustResize"
  >
  <intent-filter>
    <action android:name="android.intent.action.MAIN"/>
    <category android:name="android.intent.category.LAUNCHER"/>
  </intent-filter>
</activity>
```

New configuration:
```xml
<activity android:name="io.flutter.embedding.android.FlutterActivity"
  android:theme="@android:style/Theme.Black.NoTitleBar" 
 android:configChanges="orientation|keyboardHidden|keyboard|screenSize|locale|layoutDirection|fontScale|screenLayout|density|uiMode"
  android:hardwareAccelerated="true"
  android:windowSoftInputMode="adjustResize"
  >
  <intent-filter>
    <action android:name="android.intent.action.MAIN"/>
    <category android:name="android.intent.category.LAUNCHER"/>
  </intent-filter>
</activity>
```

5. Remove all `<meta-data>` tags with key `android:name="io.flutter.app.android.SplashScreenUntilFirstFrame"`.
6. If a launch screen/splash screen is still desired, do the following:

Add a launch theme to `styles.xml` that configures the desired launch screen as a background `Drawable`:
```xml
<!-- You can name this style whatever you'd like -->
<style name="LaunchTheme" parent="@android:style/Theme.Black.NoTitleBar">
    <item name="android:windowBackground">@drawable/[your_launch_drawable_here]</item>
</style>
```

Add a normal theme that to `styles.xml` that should replace the launch screen when the Android process is fully initialized:
```xml
<!-- You can name this style whatever you'd like -->
<style name="NormalTheme" parent="@android:style/Theme.Black.NoTitleBar">
    <item name="android:windowBackground">@drawable/[your_normal_background_drawable]</item>
</style>
```

Configure `FlutterActivity` to start with your launch theme and then shift to your normal theme as follows:
```xml
<activity android:name="io.flutter.embedding.android.FlutterActivity"
  android:theme="@style/LaunchTheme"
  // some code omitted
  >
  <meta-data
    android:name="io.flutter.embedding.android.NormalTheme"
    android:resource="@style/NormalTheme"
    />

  <!-- some code omitted -->
</activity>
```


7. Add a new `<meta-data>` tag under `<application>` with the following content:
```xml
<meta-data
    android:name="flutterEmbedding"
    android:value="2" />
```

Your app should still build as normal (such as via `flutter build apk`) but you're now using the new Android classes. 

# Add-to-app migration

TODO