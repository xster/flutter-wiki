Making it easy to add Flutter to an existing app is work in progress, tracked by [#14821](https://github.com/flutter/flutter/issues/14821). This page documents ongoing experiments and will be updated as we find more and better ways to do this, and as we build out tooling to aid.

Status: draft

# Recommendations

None yet, we're still experimenting.

# Experiments, Android

## Turn the Flutter Project into a Module
The following experiment allows to use a Flutter project as an Android activity. The Android and Flutter projects live in separate directories.

This approach works best if the communication between the existing Android application and the Flutter activity is limited and the Flutter activity can be developed as a separate application first. With the given instructions, the combined application does not support hot-reload.

Fundamentally, a Flutter application is turned into an Android activity. The existing Android project declares a dependency on the Flutter activity which is then automatically built and pulled in during the build process of the Android application.

### In the Flutter Project
The gradle library for Flutter currently assumes that it deals with an Application extension. We need to change one file in the flutter repository to make it work for supporting libraries:

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
Minor note: the android property of the project object is a decorated object.

### On the Flutter Side
As a simplification, we don't implement any communication between the container app and the Flutter activity. It would be trivial to support intent-based messages, but other forms of communications are probably simple as well.

We start by creating a fresh Flutter project:
```
ᐅ flutter create flutter_part
```

Since we want to merge two Android projects it's recommended (required?) to change the directory-name of the Flutter Android application. I chose `flutter_part`. While not necessary (things worked without it) it's recommended to change the `build.gradle`'s `project.evaluationDependsOn` to the new name.

Changing an application to become an activity library is done in the `build.gradle` file:
``` diff
diff --git a/android/flutter_part/build.gradle b/android/flutter_part/build.gradle
index 5dfc491..206c8c2 100644
--- a/android/flutter_part/build.gradle
+++ b/android/flutter_part/build.gradle
@@ -11,7 +11,7 @@ if (flutterRoot == null) {
     throw new GradleException("Flutter SDK not found. Define location with flutter.sdk in the local.properties file.")
 }
 
-apply plugin: 'com.android.application'
+apply plugin: 'com.android.library'
 apply from: "$flutterRoot/packages/flutter_tools/gradle/flutter.gradle"
 
 android {
@@ -23,7 +23,9 @@ android {
 
     defaultConfig {
         // TODO: Specify your own unique Application ID (https://developer.android.com/studio/build/application-id.html).
-        applicationId "com.example.flutterpart"
+
+        // Libraries must not have `applicationId`s.
+        // applicationId "com.example.flutterpart"
         minSdkVersion 16
         targetSdkVersion 27
         versionCode 1
```

At this point we can also remove the `android:label` from the `<application>` tag in Flutters `AndroidManifest.xml`. This is not required, because the Android app can force an overwrite of that label, but it requires more work on the other side.

``` diff
diff --git a/android/flutter_part/src/main/AndroidManifest.xml b/android/flutter_part/src/main/AndroidManifest.xml
index 9686b2f..6430571 100644
--- a/android/flutter_part/src/main/AndroidManifest.xml
+++ b/android/flutter_part/src/main/AndroidManifest.xml
@@ -14,7 +14,6 @@
         FlutterApplication and put your custom class here. -->
    <application
        android:name="io.flutter.app.FlutterApplication"
-        android:label="flutter_part"
        android:icon="@mipmap/ic_launcher">
        <activity
            android:name=".MainActivity"
```

## On the Android Side
This section explains how the existing Android application can use the Flutter activity.

For simplicity we assume a fresh Android application (with a button) that has been created using Android Studio.
The minimal requirement for the Android application must be set to the same (or higher) minimal requirement as Flutter (minSdkVersion: 16).

The Flutter project must get referenced in the `settings.gradle` file:
```
include ':app', ':flutter_part'
project(':flutter_part').projectDir = new File(settingsDir, '../flutter_part/android/flutter_part')
```
(This assumes that the Flutter project lives in a directory adjacent to the Android application.)

Similarly, we need to change the application's gradle file so that it depends on the library:

``` diff
diff --git a/app/build.gradle b/app/build.gradle
index 1ec1b83..58d5f8d 100644
--- a/app/build.gradle
+++ b/app/build.gradle
@@ -16,6 +16,10 @@ android {
            proguardFiles getDefaultProguardFile('proguard-android.txt'), 'proguard-rules.pro'
        }
    }
+
+    dependencies {
+        implementation project(":flutter_part")
+    }
}
 
dependencies {
```

Finally, before being able to compile, we need to tell the Android application where Flutter is installed by updating the local.properties file:
```
flutter.sdk=/usr/local/google/home/floitsch/code/flutter/flutter
```
This line was copied from the Flutter directory's `local.properties` file.

After that the Flutter activity can be launched like any other Android activity:
``` Java
    private void startFlutterActivity(View view) {
        ComponentName componentName = new ComponentName(this, "com.example.flutterpart.MainActivity");
        Intent intent = new Intent().setComponent(componentName);
        startActivity(intent);
    }
```