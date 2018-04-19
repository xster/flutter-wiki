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