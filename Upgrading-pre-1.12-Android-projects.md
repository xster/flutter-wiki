_If you `flutter create`d your project prior to version 1.12, this may apply to your project_

# Background

In order to better support the execution environments of adding Flutter to an existing project, the old Android platform-side wrappers hosting the Flutter runtime at [`io.flutter.app.FlutterActivity`](https://github.com/flutter/engine/blob/master/shell/platform/android/io/flutter/app/FlutterActivity.java) and their associated classes are now deprecated. New wrappers at [`io.flutter.embedding.android.FlutterActivity`](https://github.com/flutter/engine/blob/master/shell/platform/android/io/flutter/embedding/android/FlutterActivity.java) and associated classes now replace them. 

Those classes better support real world scenarios where the FlutterActivity isn't the first and only Android Activity in an application. 

# Motivation

Your existing full-Flutter projects aren't immediately affected and will continue to work as before for the foreseeable future. 

However, the new Android wrappers also introduces a new set of Android plugins development APIs. Plugins developed exclusively on the new plugins API will not work on older pre-1.12 Android projects. Building a pre-1.12 Android project that uses plugins created after 1.12 will yield a build-time error unless the plugin developer explicitly opted to create a second backward compatible implementation. 

# Full-Flutter app migration
 
_This guide assumes you haven't manually modified your Android host project for your Flutter project. If you did, consult the add-to-app migration guide below._ 

If you opt to migrate your standard `flutter create`d project, follow the following steps:

1. Open android/app/src/main/java/[your.package.name]/MainActivity.java.
1. Change the existing code 
```java
package [your.package.name];

import android.os.Bundle;
import io.flutter.app.FlutterActivity;
import io.flutter.plugins.GeneratedPluginRegistrant;

public class MainActivity extends FlutterActivity {
  @Override
  protected void onCreate(Bundle savedInstanceState) {
    super.onCreate(savedInstanceState);
    GeneratedPluginRegistrant.registerWith(this);
  }
}
```

to 

```java
package [your.package.name];

import io.flutter.embedding.android.FlutterActivity;

public class MainActivity extends FlutterActivity { }
```
3. Open android/app/src/main/AndroidManifest.xml.
4. Remove all `<meta-data>` tags with key `android:name="io.flutter.app.android.SplashScreenUntilFirstFrame"`.
5. Add a new `<meta-data>` tag under `<application>` with content
```xml
<meta-data
    android:name="flutterEmbedding"
    android:value="2" />
```

Your app should still build as normal (such as via `flutter build apk`) but you're now using the new Android classes. 

# Add-to-app migration

TODO