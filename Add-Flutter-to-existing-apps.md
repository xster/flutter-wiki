Making it easy to add Flutter to an existing app is work in progress, tracked by [#14821](https://github.com/flutter/flutter/issues/14821). This page documents ongoing experiments and will be updated as we find more and better ways to do this, and as we build out tooling to aid.

Status: draft

# Recommendations

None yet, we're still experimenting.

# Experiments, Android

## Turn the Flutter Project into a Module
The following experiment allows to use a Flutter project as an Android activity. The Android and Flutter projects live in separate directories.

This approach works best if the communication between the existing Android application and the Flutter activity is limited. Also, the combined application does not support hot-reload (with the given instructions) and it's therefore more convenient if the Flutter activity can be developed as a separate application first.

Fundamentally, a Flutter application is turned into an Android activity. The existing Android project declares a dependency on the Flutter activity which is then automatically built and pulled in during the build process of the Android application.

### In the Flutter Tools Project
The gradle library for Flutter assumed that it dealt with an Application extension. This has been fixed on Master (with [731ed1b](https://github.com/flutter/flutter/commit/731ed1b11b388f714eaeb3ed03969f27d195e1a2)). Since this version hasn't been released yet, users might need to apply the following patch to their Flutter tools:

``` diff
diff --git a/packages/flutter_tools/gradle/flutter.gradle b/packages/flutter_tools/gradle/flutter.gradle
index c421a64b9..fdde2d4c9 100644
--- a/packages/flutter_tools/gradle/flutter.gradle
+++ b/packages/flutter_tools/gradle/flutter.gradle
@@ -281,7 +281,7 @@ class FlutterPlugin implements Plugin<Project> {
            targetPlatformValue = project.property('target-platform')
        }
 
-        project.android.applicationVariants.all { variant ->
+        def addFlutterDeps = { variant ->
            String flutterBuildMode = buildModeFor(variant.buildType)
            if (flutterBuildMode == 'debug' && project.tasks.findByName('flutterBuildX86Jar')) {
                Task task = project.tasks.findByName("compile${variant.name.capitalize()}JavaWithJavac")
@@ -338,6 +338,11 @@ class FlutterPlugin implements Plugin<Project> {
            }
            variant.outputs[0].processResources.dependsOn(copyFlxTask)
        }
+        if (project.android.hasProperty("applicationVariants")) {
+            project.android.applicationVariants.all addFlutterDeps
+        } else {
+            project.android.libraryVariants.all addFlutterDeps
+        }
    }
}
```

### On the Flutter Side
As a simplification, we don't implement any communication between the container app and the Flutter activity. It would be trivial to support intent-based messages, but other forms of communications are probably simple as well.

We start by creating a fresh Flutter project:
```
ᐅ flutter create flutter_part
```

Changing an application to become an activity library is done in the `build.gradle` file:
``` diff
diff --git a/android/app/build.gradle b/android/app/build.gradle
index 5dfc491..c903d8f 100644
--- a/android/app/build.gradle
+++ b/android/app/build.gradle
@@ -11,7 +11,7 @@ if (flutterRoot == null) {
     throw new GradleException("Flutter SDK not found. Define location with flutter.sdk in the local.properties file.")
 }
 
-apply plugin: 'com.android.application'
+apply plugin: 'com.android.library'
 apply from: "$flutterRoot/packages/flutter_tools/gradle/flutter.gradle"
 
 android {
@@ -22,8 +22,6 @@ android {
     }
 
     defaultConfig {
-        // TODO: Specify your own unique Application ID (https://developer.android.com/studio/build/application-id.html).
-        applicationId "com.example.flutterpart"
         minSdkVersion 16
         targetSdkVersion 27
         versionCode 1
```

At this point we can also remove the `android:name`, `android:label` and `android:icon` from the `<application>` tag in Flutters `AndroidManifest.xml`. This is not required, because the Android app can force an overwrite of these attributes, but it requires more work on the other side.

Also, we remove the `intent-filter` entry, since we don't expect the Flutter activity to be launched directly.

``` diff
diff --git a/android/app/src/main/AndroidManifest.xml b/android/app/src/main/AndroidManifest.xml
index 9686b2f..65fe00b 100644
--- a/android/app/src/main/AndroidManifest.xml
+++ b/android/app/src/main/AndroidManifest.xml
@@ -12,10 +12,7 @@
          In most cases you can leave this as-is, but you if you want to provide
          additional functionality it is fine to subclass or reimplement
          FlutterApplication and put your custom class here. -->
-    <application
-        android:name="io.flutter.app.FlutterApplication"
-        android:label="flutter_part"
-        android:icon="@mipmap/ic_launcher">
+    <application>
         <activity
             android:name=".MainActivity"
             android:launchMode="singleTop"
@@ -30,10 +27,6 @@
             <meta-data
                 android:name="io.flutter.app.android.SplashScreenUntilFirstFrame"
                 android:value="true" />
-            <intent-filter>
-                <action android:name="android.intent.action.MAIN"/>
-                <category android:name="android.intent.category.LAUNCHER"/>
-            </intent-filter>
         </activity>
     </application>
 </manifest>
```
Since the `FlutterApplication` was responsible for initializing the system, we now need to add an initialization call to the `onCreate` method of the Activity (`android/app/src/main/java/com/example/flutterpart/MainActivity.java`):

``` Java
public class MainActivity extends FlutterActivity {
  @Override
  protected void onCreate(Bundle savedInstanceState) {
    io.flutter.view.FlutterMain.startInitialization(this.getApplicationContext());  // <= New.
    super.onCreate(savedInstanceState);
    GeneratedPluginRegistrant.registerWith(this);
  }
}
```

## On the Android Side
This section explains how the existing Android application can use the Flutter activity.

For simplicity we assume a fresh Android application (with a button) that has been created using Android Studio.
The minimal requirement for the Android application must be set to the same (or higher) minimal requirement as Flutter (minSdkVersion: 16).

The Flutter project must get referenced in the `settings.gradle` file:
```
include ':app', ':flutter_part'
project(':flutter_part').projectDir = new File(settingsDir, '../flutter_part/android/app')
```
(This assumes that the Flutter project lives in a directory adjacent to the Android application.)

At this point the project won't compile anymore because the Flutter module needs to know where the Flutter project is located. We fix this by updating the `local.properties` file:

```
flutter.sdk=/usr/local/google/home/floitsch/code/flutter/flutter
```
This line was copied from the Flutter directory's `local.properties` file.

Now, we need to change the application's gradle file so that it depends on the library:

``` diff
diff --git a/app/build.gradle b/app/build.gradle
index 000886d..82dd2f3 100644
--- a/app/build.gradle
+++ b/app/build.gradle
@@ -26,4 +26,5 @@ dependencies {
     testImplementation 'junit:junit:4.12'
     androidTestImplementation 'com.android.support.test:runner:1.0.1'
     androidTestImplementation 'com.android.support.test.espresso:espresso-core:3.0.1'
+    implementation project(":flutter_part")
 }
```

After that the Flutter activity can be launched like any other Android activity:
``` Java
    private void startFlutterActivity(View view) {
        ComponentName componentName = new ComponentName(this, "com.example.flutterpart.MainActivity");
        Intent intent = new Intent().setComponent(componentName);
        startActivity(intent);
    }
```

### Hot Reload / Restart
Flutter can hot reload and restart code inside an existing Android application. Invoking `flutter run` with the `--use-application-binary` flag launches the Android application and connects to the Flutter VM once it is available.

However, before this works we need to make sure that the application is compiled in the mode that `flutter run` expects. Specifically, `flutter run` is (currently) using the `dart-preview-2` flag, whereas a simple compilation with `gradle` doesn't include this flag by default.

In the existing Android project's `gradle.properties` file add the following line:
```
preview-dart-2=true
```

This flag will become unnecessary, once the Dart 2 semantics is always on by default.

Make sure that the application is rebuilt. (The easiest is usually to run `./gradlew assembleDebug`).

In the flutter-part directory, run Flutter with the existing Android application as follows:

```
flutter run --use-application-binary ../MyApplication/app/build/outputs/apk/debug/app-debug.apk
```

This assumes that the existing Android application is named `MyApplication` and is located directly next to the Flutter directory. Once the application launches, navigate to the Flutter activity, at which point Flutter will automatically connect to the VM.

Note that the Android application includes a snapshot of the Flutter activity. As long as the application is not recompiled it will start up with that state. However, a restart (pressing "R") will upload the latest code.

### Plugins
TBD.

### Troubleshooting
Here are common error messages and how they can be fixed.

---

```
Unable to resolve dependency for ':app@debug/compileClasspath': Could not resolve project :flutter_part.
```
This error message happens when the Flutter project is still an application. Make sure to update the `android/app/build.gradle`.

---

```
Library projects cannot set applicationId. applicationId is set to 'com.example.flutterpart' in default config.
```
As explained by the error message: Library projects cannot have `applicationId`s. Just delete that line in `android/app/build.gradle`.

---

```
Manifest merger failed : Attribute application@label value=(@string/app_name) from AndroidManifest.xml:8:9-41
	is also present at [:flutter_part] AndroidManifest.xml:27:9-37 value=(flutter_part).
	Suggestion: add 'tools:replace="android:label"' to <application> element at AndroidManifest.xml:5:5-22:19 to override.
```

This happens when the flutter-part's `AndroidManifest.xml` still contains `android:name`, `android:label` and `android:icon` attributes in the `<application>` tag. Either remove them, or override them (as suggested in the error message).

---

```
Element type "application" must be followed by either attribute specifications, ">" or "/>".
```

This happens when deleting `android:name`, `android:label` and `android:icon` from the `AndroidManifest.xml` file without leaving the closing `>` for the `application` tag. Just add the `>` where you deleted 
these attributes.

---

Two launcher entries are added when the Android application is installed.

This happens when the `AndroidManifest.xml` still contains the `<intent-filter>` entry.

---

When invoking `flutter run` with the Android application's `apk` the application starts in the Flutter app.

This happens when the `AndroidManifest.xml` still contains the `<intent-filter>` entry. In that case `flutter run` immediately launches the Flutter activity.

---

```
04-19 17:20:29.404 18661-18661/com.example.floitsch.myapplication E/FlutterMain: Flutter initialization failed.
    java.lang.NullPointerException: Attempt to invoke virtual method 'void io.flutter.view.ResourceExtractor.waitForCompletion()' on a null object reference
        at io.flutter.view.FlutterMain.ensureInitializationComplete(FlutterMain.java:184)
        at io.flutter.app.FlutterActivityDelegate.onCreate(FlutterActivityDelegate.java:152)
        at io.flutter.app.FlutterActivity.onCreate(FlutterActivity.java:81)
        at com.example.flutterpart.MainActivity.onCreate(MainActivity.java:11)
```

This error indicates that the `Flutter` engine wasn't initialized yet. Make sure that the Flutter activity's `onCreate` starts with `io.flutter.view.FlutterMain.startInitialization(this.getApplicationContext());`.

---

```
04-19 17:23:25.351 19107-19107/com.example.floitsch.myapplication E/AndroidRuntime: FATAL EXCEPTION: main
    Process: com.example.floitsch.myapplication, PID: 19107
    android.content.ActivityNotFoundException: Unable to find explicit activity class {com.example.floitsch.myapplication/com.example.flutterpart.MainActivity}; have you declared this activity in your AndroidManifest.xml?
        at android.app.Instrumentation.checkStartActivityResult(Instrumentation.java:1933)
        at android.app.Instrumentation.execStartActivity(Instrumentation.java:1616)
        at android.app.Activity.startActivityForResult(Activity.java:4487)
```

This error message can have two reasons:
1. the `build.gradle` file is missing the dependency entry (`implementation project(":flutter_part")`), or
2. the (qualified) name of the activity is different.

---

When invoking `flutter run --use-application-binary`.

```
Hot reload was rejected:
Dart_NewStringFromUTF8 expects argument 'str' to be valid UTF-8.
```
or
```
W/flutter (19559): [WARNING:flutter/runtime/dart_controller.cc(194)] Failed to load package map: /data/user/0/com.example.floitsch.myapplication/cache/flutter_partJIFUUQ/flutter_part/.packages
E/flutter (19559): [ERROR:topaz/lib/tonic/logging/dart_error.cc(16)] Dart_NewStringFromUTF8 expects argument 'str' to be valid UTF-8.
E/flutter (19559): [ERROR:topaz/lib/tonic/logging/dart_error.cc(16)] Dart_GetClosure expects argument 'library' to be non-null.
```
or (in newer versions):
```
E/flutter (19892): [ERROR:topaz/lib/tonic/logging/dart_error.cc(16)] Dart_LoadScriptFromSnapshot expects parameter 'buffer' to be a script type snapshot with a valid length.
```

These error messages happen, when the application isn't compiled with `preview-dart-2`. Make sure to add `preview-dart-2` to the Android application's `gradle.properties` and recompile the Android application.

# Experiments/iOS
With the right setup Flutter can be used as a UIViewController embedded in an existing app.

The following steps integrate a Flutter app `embedded` (with a layout like that of `flutter create`) into an existing iOS app `embedder`.

### Disable bitcode
Currently the flutter aot compiled code is not compatible with bitcode.

Go to the project view of `embedder` and choose the build target. Go to the "build settings" tab. Find "Enable bitcode" and switch to "no".

### Create a Flutter group/folder
The flutter compilation will place artifacts here.

Right-click the `embedder` project and choose "new Group" name the group Flutter. 

### Create and use a flutter settings .xcconfig file
The settings file contains information about the location of the flutter installation, the flutter framework (contains the engine) and the flutter app you want to integrate. It also contains the build mode (debug/profile/release).

For a flutter project `flutter build` creates this settings file automatically (`Generated.xcconfig`). For this scenario we have to create and maintain it manually. 

Right click the "Flutter" group and choose "new file" choose the "configuration settings file", give it a name (eg. FlutterConfig.xcconfig). Replace the template with something like:
```
FLUTTER_ROOT=/path/to/flutter
FLUTTER_APPLICATION_PATH=/path/to//embedded
FLUTTER_TARGET=/path/to/embedded/lib/main.dart
FLUTTER_BUILD_MODE=debug // replace with "release" for running the flutter app in release mode.
FLUTTER_BUILD_DIR=build
SYMROOT=${SOURCE_ROOT}/../build/ios
FLUTTER_FRAMEWORK_DIR=/path/to/flutter/bin/cache/artifacts/engine/ios // replace with ios-release for running the flutter app in release mode
PREVIEW_DART_2=true
```
An easy way to get the paths right to copy it from `embedded` (after running it with `flutter run`).


Now this configuration file must be included in your existing xcconfig file with the line:
```
#include "Flutter/FlutterConfig.xccfonfig"
```

If you have no existing xcconfig file for your target you can create one (for example `Debug.xcconfig`) and point your target to it by going to the project view, choosing the project, take the "info" tab, go to the "Configurations" section, and choose `Debug` for the target.

### Create a `AppFrameworkInfo.plist` file
_This step is no longer needed after https://github.com/flutter/flutter/pull/18358)._

This file is copied into the `app.framework` that contains the code snapshot.

Right click the "Flutter" folder, "New File" , "Property List", name it `AppFrameworkInfo.plist`. Give it the following contents:
```
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE plist PUBLIC "-//Apple//DTD PLIST 1.0//EN" "http://www.apple.com/DTDs/PropertyList-1.0.dtd">
<plist version="1.0">
    <dict>
        <key>CFBundleDevelopmentRegion</key>
        <string>en</string>
        <key>CFBundleExecutable</key>
        <string>App</string>
        <key>CFBundleIdentifier</key>
        <string>io.flutter.flutter.app</string>
        <key>CFBundleInfoDictionaryVersion</key>
        <string>6.0</string>
        <key>CFBundleName</key>
        <string>App</string>
        <key>CFBundlePackageType</key>
        <string>FMWK</string>
        <key>CFBundleShortVersionString</key>
        <string>1.0</string>
        <key>CFBundleSignature</key>
        <string>????</string>
        <key>CFBundleVersion</key>
        <string>1.0</string>
        <key>UIRequiredDeviceCapabilities</key>
        <array>
            <string>arm64</string>
        </array>
        <key>MinimumOSVersion</key>
        <string>8.0</string>
    </dict>
</plist>
```

### Make a build-step that calls `xcode-backend.sh`
The `xcode-backend.sh` script compiles the flutter app, and copies the right engine to the "Flutter" folder.

In the project view, choose the target, go to the "Build Phases" tab, press "+" in the upper left corner. Choose "New Run Script Phase". Change the script to `/bin/sh "$FLUTTER_ROOT/packages/flutter_tools/bin/xcode_backend.sh" build`. Drag the phase to be after "Target dependencies" or "Check Pods Manifest.lock".

### Add the Flutter artifacts to the project.
First build the target. This will fail, but before failing it will run `xcode-backend.sh`.
Right click the "Flutter" folder. Choose "Add Files to "embedded"". Mark the files/folders `app.framework`, `flutter.framework` and `flutter_assets`.

Now go to the project view, and choose the target. Go to the "General" tab and in the "Embedded Binaries" section, press "+" and choose the "app.framework" and "flutter.framework" frameworks.

### Make your ApplicationDelegate a FlutterApplicationDelegate
This 

Make your ApplicationDelegate inherit from the FlutterApplicationDelegate, something akin to (`AppDelegate.h`):

```
#import <UIKit/UIKit.h>
#import <Flutter/Flutter.h>

@interface AppDelegate : FlutterAppDelegate <UIApplicationDelegate>


@end
```

If the current AppDelegate is already inheriting from another class you can copy code from 
[FlutterAppDelegate.mm](https://github.com/flutter/engine/blob/master/shell/platform/darwin/ios/framework/Source/FlutterAppDelegate.mm).

### Use a FlutterViewController in your App
Now you can insert a FlutterViewController somewhere in your code (or in a storyboard).
The following code demonstrates pushing a FlutterViewController to the current NavigationController, and receiving messages from Flutter.

```
#include <Flutter/Flutter.h>

...

- (void)pushFlutter {
    if (!_flutterViewController) {
       _flutterViewController = [[FlutterViewController alloc] initWithProject:nil nibName:nil bundle:nil];
       messageChannel = [FlutterBasicMessageChannel messageChannelWithName:@"channel"
                                                           binaryMessenger:_flutterViewController
                                                                      codec:[FlutterStandardMessageCodec sharedInstance]];
        UINavigationController*  __weak weakSelf = self;
        [messageChannel setMessageHandler:^(id message, FlutterReply reply) {
            // Any message on this channel pops the Flutter view.
            [[weakSelf navigationController] popViewControllerAnimated:YES];
            reply(@"");
        }];
    }
    NSAssert([self navigationController], @"Must have a NaviationController");
    [[self navigationController]  pushViewController:_flutterViewController2 animated:YES];
}
```
The Dart side can use the channel like:
```
import 'package:flutter/services.dart';

BasicMessageChannel channel =
    new BasicMessageChannel("channel", new StandardMessageCodec());

void pop() {
  channel.send({});
}
```

If you use several Flutter views  you can use `[_flutterViewController setInitialRoute:@"route/for/view"]` and dispatch from Dart.

Now you should be able to run the project from xCode!

### Running with hot reload enabled.
Compile the project to an app (remember to put it in debug-mode from the xcconfig file to enable hot reload).
From the command-line you should be able to do something like from the folder of `embedder`:

```
/usr/bin/env xcrun xcodebuild build -configuration Debug ONLY_ACTIVE_ARCH=YES VERBOSE_SCRIPT_LOGGING=YES -project embedder.xcodeproj -scheme embedder BUILD_DIR=build/ios -sdk iphoneos -arch arm64
```

And then (from inside the folder of `embedded`) run:
```
flutter run --use-application-binary /path/to/embedder/build/ios/Debug-iphoneos/embedder.app
```

### Plugins
TBD.