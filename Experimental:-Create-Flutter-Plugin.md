# Quick Migration Steps

These instructions are for the ecosystem team as they implement parallel plugin support for v2 embedding and be testable on Firebase TestLab. See current version of the `battery` plugin for an example: https://github.com/flutter/plugins/tree/master/packages/battery

1. Update the main plugin class (`*Plugin.java`) to implement `FlutterPlugin`. For more complex plugins, you can separate the `FlutterPlugin` and `MethodCallHandler` into two classes. See the next section, **Basic Plugin**, for more details on accessing app resources with the v2 embedding. 

Also, note that the plugin should still contain the static `registerWith()` method to remain compatible with apps that don't use v2 embedding.

In addition, you should document all non-overridden public members within the plugin. In an add-to-app scenario, these classes will be accessible to a developer and require documentation.

2. **(Optional)** If your plugin needs an `Activity` reference, also implement `ActivityAware`.

3. **(Optional)** If your plugin is expected to be held in a background `Service` at any point in time, implement `ServiceAware`.

4. Update example app `MainActivity.java` to use the v2 embedding `FlutterActivity`. e.g.

```
package io.flutter.plugins.firebasecoreexample;

import io.flutter.embedding.android.FlutterActivity;
import io.flutter.embedding.engine.FlutterEngine;
import io.flutter.plugins.firebase.core.FirebaseCorePlugin;

public class MainActivity extends FlutterActivity {
  // TODO(<github-username>): Remove this once v2 of GeneratedPluginRegistrant rolls to stable. https://github.com/flutter/flutter/issues/42694
  @Override
  public void configureFlutterEngine(FlutterEngine flutterEngine) {
    flutterEngine.getPlugins().add(new FirebaseCorePlugin());
  }
}
```

5. **(Optional)** Use `ShimPluginRegistry` to add plugins that don’t yet support the v2 embedding. e.g.
```
ShimPluginRegistry shimPluginRegistry = new ShimPluginRegistry(flutterEngine);
PathProviderPlugin.registerWith(
        shimPluginRegistry.registrarFor("io.flutter.plugins.pathprovider.PathProviderPlugin"));
VideoPlayerPlugin.registerWith(
        shimPluginRegistry.registrarFor("io.flutter.plugins.videoplayer.VideoPlayerPlugin"));
```

6. Create an `EmbeddingV1Activity.java` that uses the v1 embedding in the same folder as `MainActivity`. e.g.
```
package io.flutter.plugins.firebasecoreexample;

import android.os.Bundle;
import io.flutter.app.FlutterActivity;
import io.flutter.plugins.GeneratedPluginRegistrant;

public class EmbeddingV1Activity extends FlutterActivity {
 @Override
 protected void onCreate(Bundle savedInstanceState) {
   super.onCreate(savedInstanceState);
   GeneratedPluginRegistrant.registerWith(this);
 }
}
```

7. Add the `EmbeddingV1Activity` to the **<plugin_name>/example/android/app/src/main/AndroidManifest.xml**. e.g.
```
<activity
    android:name=".EmbeddingV1Activity"
    android:theme="@style/LaunchTheme"
         android:configChanges="orientation|keyboardHidden|keyboard|screenSize|locale|layoutDirection|fontScale"
    android:hardwareAccelerated="true"
    android:windowSoftInputMode="adjustResize">
</activity>

```

8. To have the plugin support Flutter on branches master and stable, include this gradle script in **<plugin_name>/android/build.gradle**.
```
// TODO(<github-username>): Remove this hack once androidx.lifecycle is included on stable. https://github.com/flutter/flutter/issues/42348
afterEvaluate {
    def containsEmbeddingDependencies = false
    for (def configuration : configurations.all) {
        for (def dependency : configuration.dependencies) {
            if (dependency.group == 'io.flutter' &&
                    dependency.name.startsWith('flutter_embedding') &&
                    dependency.isTransitive())
            {
                containsEmbeddingDependencies = true
                break
            }
        }
    }
    if (!containsEmbeddingDependencies) {
        android {
            dependencies {
                def lifecycle_version = "1.1.1"
                api "android.arch.lifecycle:runtime:$lifecycle_version"
                api "android.arch.lifecycle:common:$lifecycle_version"
                api "android.arch.lifecycle:common-java8:$lifecycle_version"
            }
        }
    }
}
```

9. Update **<plugin_name>/example/android/app/build.gradle** with androidX tests dependencies
```
androidTestImplementation 'androidx.test:runner:1.2.0'
androidTestImplementation 'androidx.test:rules:1.2.0'
androidTestImplementation 'androidx.test.espresso:espresso-core:3.2.0'
```

10. Add tests files for `MainActivity` and `EmbeddingV1Activity` in **<plugin_name>/example/android/app/src/androidTest/java/<plugin-path>/**. You will need to create these directories. e.g.
```
package io.flutter.plugins.firebase.core;

import androidx.test.rule.ActivityTestRule;
import dev.flutter.plugins.e2e.FlutterRunner;
import io.flutter.plugins.firebasecoreexample.MainActivity;
import org.junit.Rule;
import org.junit.runner.RunWith;

@RunWith(FlutterRunner.class)
public class MainActivityTest {
  @Rule public ActivityTestRule<MainActivity> rule = new ActivityTestRule<>(MainActivity.class);
}
```
```
package io.flutter.plugins.firebase.core;

import androidx.test.rule.ActivityTestRule;
import dev.flutter.plugins.e2e.FlutterRunner;
import io.flutter.plugins.firebasecoreexample.EmbeddingV1Activity;
import org.junit.Rule;
import org.junit.runner.RunWith;

@RunWith(FlutterRunner.class)
public class EmbeddingV1ActivityTest {
  @Rule
  public ActivityTestRule<EmbeddingV1Activity> rule =
      new ActivityTestRule<>(EmbeddingV1Activity.class);
}
```

11. Add `e2e` and `flutter_driver` dev_dependencies to **<plugin_name>/pubspec.yaml** and **<plugin_name>/example/pubspec.yaml**.
```
e2e: ^0.2.1
flutter_driver:
  sdk: flutter
```

12. Update minimum Flutter version of environment in **<plugin_name>/pubspec.yaml**. All plugins moving forward will set the the minimum version to `1.9.1+hotfix.4` which is the minimum version we can guarantee support for .e.g.
```
environment:
  sdk: ">=2.0.0-dev.28.0 <3.0.0"
  flutter: ">=1.9.1+hotfix.5 <2.0.0"
```

13. Create a simple test in **<plugin_name>/test/<plugin_name>_e2e.dart.** For the purpose of testing the PR that adds the v2 embedding support, we're trying to test some very basic functionality of the plugin. This is a smoke test to ensure that the plugin properly registers with the new embedder. e.g.
```
import 'package:flutter_test/flutter_test.dart';
import 'package:battery/battery.dart';
import 'package:e2e/e2e.dart';

void main() {
  E2EWidgetsFlutterBinding.ensureInitialized();

  testWidgets('Can get battery level', (WidgetTester tester) async {
    final Battery battery = Battery();
    final int batteryLevel = await battery.batteryLevel;
    expect(batteryLevel, isNotNull);
  });
}
```

14. Test run the e2e tests locally. In a terminal:
```
cd <plugin_name>/example
flutter build apk
cd android
./gradlew app:connectedAndroidTest -Ptarget=`pwd`/../../test/<plugin_name>_e2e.dart
```

# Basic Plugin

To get started with a Flutter Android plugin in code, start by implementing `FlutterPlugin`.

```java
public class MyPlugin implements FlutterPlugin {
  @Override
  public void onAttachedToEngine(@NonNull FlutterPluginBinding binding) {
    // TODO: your plugin is now attached to a Flutter experience.
  }

  @Override
  public void onDetachedFromEngine(@NonNull FlutterPluginBinding binding) {
    // TODO: your plugin is no longer attached to a Flutter experience.
  }
}
``` 

As shown above, your plugin may or may not be associated with a given Flutter experience at any given moment in time. You should take care to initialize your plugin's behavior in `onAttachedToEngine()`, and then cleanup your plugin's references in `onDetachedFromEngine()`.

The `FlutterPluginBinding` provides your plugin with a few important references:
* `binding.getFlutterEngine()`: Returns the `FlutterEngine` that your plugin is attached to, providing access to components like the `DartExecutor`, `FlutterRenderer`, and more.
* `binding.getApplicationContext()`: Returns the Android application's `Context` for the running app.
* `binding.getLifecycle()`: Returns a reference that can be used to obtain a `Lifecycle` object. If you need to use this lifecycle reference then you need add a project dependency on Flutter's Android lifecycle package.

# UI/Activity Plugin

If your plugin needs to interact with the UI, such as requesting permissions, or altering Android UI chrome, then you need to take additional steps to define your plugin. You must implement the `ActivityAware` interface.

```java
public class MyPlugin implements FlutterPlugin, ActivityAware {
  //...normal plugin behavior is hidden...

  @Override
  public void onAttachedToActivity(ActivityPluginBinding activityPluginBinding) {
    // TODO: your plugin is now attached to an Activity
  }

  @Override
  public void onDetachedFromActivityForConfigChanges() {
    // TODO: the Activity your plugin was attached to was destroyed to change configuration.
    // This call will be followed by onReattachedToActivityForConfigChanges().
  }

  @Override
  public void onReattachedToActivityForConfigChanges(ActivityPluginBinding activityPluginBinding) {
    // TODO: your plugin is now attached to a new Activity after a configuration change.
  }

  @Override
  public void onDetachedFromActivity() {
    // TODO: your plugin is no longer associated with an Activity. Clean up references.
  }
}
``` 

To interact with an `Activity`, your `ActivityAware` plugin must implement appropriate behavior at 4 stages. First, your plugin is attached to an `Activity`. You can access that `Activity` and a number of its callbacks through the provided `ActivityPluginBinding`.

Since `Activity`s can be destroyed during configuration changes, you must cleanup any references to the given `Activity` in `onDetachedFromActivityForConfigChanges()`, and then re-establish those references in `onReattachedToActivityForConfigChanges()`.

Finally, in `onDetachedFromActivity()` your plugin should clean up all references related to `Activity` behavior and return to a non-UI configuration.

# Service Plugin
TODO

# ContentProvider Plugin
TODO