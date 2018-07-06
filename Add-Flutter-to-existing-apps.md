Making it easy to add Flutter to an existing app is work in progress, tracked by [#14821](https://github.com/flutter/flutter/issues/14821). This page documents ongoing experiments and will be updated as we find more and better ways to do this, and as we build out tooling to aid.

This support is in preview, and is generally only available in the master channel.

# Recommendations
*As this is work in progress, recommendations change frequently at the moment. You may want to wait until some of the dust has settled.*

2018-07-02 Added details for using flutter attach to get a hot reload-based workflow.

2018-06-22 A preview of the new Flutter module template is now available for Android. Follow the [guide below][android-module-preview] to try it out.

2018-06-20 You may want to wait for the new Flutter module template of [the flutter_module branch](https://github.com/flutter/flutter/tree/flutter_module) to land on Flutter master. We're getting closer! Today, [#18633](https://github.com/flutter/flutter/pull/18633) landed on Flutter master, bringing Flutter tooling support for plugins when adding Flutter as a library to an Android app as detailed in the [experimental guide below][android-experiment]. The Flutter module will make the process much easier, and bring support for adding Flutter Views rather than just Activities.

2018-06-11 iOS: Follow the [experimental guide below][ios-experiment].

2018-04-09 Android: Follow the [experimental guide below][android-experiment].

# Android

## Preview: Use the Flutter module template
*Applies to work off the flutter/flutter master branch after [#18697](https://github.com/flutter/flutter/pull/18697) which landed on 2018-06-22. The functionality described below is in early, bleeding-edge **preview**: Until it has landed on the beta branch, it is subject to change without warning.*

### Create a Flutter module
Let's assume you have an existing Android app at `some/path/MyApp`, and that you want
your Flutter project as a sibling:
```
$ cd some/path/
$ flutter create -t module my_flutter
```
This creates a `some/path/my_flutter/` Flutter module project with some Dart code
to get you started and a `.android/` subfolder that wraps up the module project in
an Android library.

### Make the host app depend on the Flutter module
Include the Flutter module as a sub-project in the host app's `settings.gradle`:
```groovy
// MyApp/settings.gradle
include ':app'                                     // assumed existing content
setBinding(new Binding([gradle: this]))                                 // new
evaluate(new File(                                                      // new
  settingsDir.parentFile,                                               // new
  'my_flutter/.android/include_flutter.groovy'                          // new
))                                                                      // new
```
The binding and script evaluation allows the Flutter module to `include` itself (as `:flutter`)
and any Flutter plugins used by the module (as `:package_info`, `:video_player`, etc) in the
evaluation context of your `settings.gradle`.
 
Introduce an `implementation` dependency on the Flutter module from your app:
```groovy
// MyApp/app/build.gradle
:
dependencies {
  implementation project(':flutter')
  :
}
```

### Use the Flutter module from your Java code

Use the Flutter module's Java API to add Flutter views to your host app. This can be done
by directly using `Flutter.createView`:
```java
// MyApp/app/src/main/java/some/package/MainActivity.java
fab.setOnClickListener(new View.OnClickListener() {
  @Override
  public void onClick(View view) {
    View flutterView = Flutter.createView(
      MainActivity.this,
      getLifecycle(),
      "route1"
    );
    FrameLayout.LayoutParams layout = new FrameLayout.LayoutParams(600, 800);
    layout.leftMargin = 100;
    layout.topMargin = 200;
    addContentView(flutterView, layout);
  }
});
```
It is also possible to create a `FlutterFragment` that takes care of lifecycle by itself:
```java
// MyApp/app/src/main/java/some/package/SomeActivity.java
fab.setOnClickListener(new View.OnClickListener() {
 @Override
 public void onClick(View view) {
   FragmentTransaction tx = getSupportFragmentManager().beginTransaction();
   tx.replace(R.id.someContainer, Flutter.createFragment("route1"));
   tx.commit();
 }
});
```
Above we use the string `"route1"` to tell the Dart code which widget to display in the
Flutter view. The `lib/main.dart` file of the Flutter module project template contains a
`switch` on the provided route string. It is up to you which route strings you want and
how to interpret them.

### Hot restart/reload, option 1: Using `flutter run`

You can now build and launch your host app in the same way that you're used to. But if you want
to hot reload changes to Dart code within it, you currently (this is work in progress) need to
launch the built apk using Flutter tooling:

```
$ cd MyApp
$ ./gradlew app:assembleDebug
$ cd ../xyz
$ flutter run --use-application-binary \
    ../MyApp/app/build/outputs/apk/debug/app-debug.apk
```

### Hot restart/reload, option 2: Using `flutter attach`

1. Run the Flutter tooling in 'attached' mode:
    ```
    cd <path to your Flutter module>
    flutter attach
    ```

1. Run your Android app as you in the same way that you're used to (e.g., by pressing Run in Android Studio).

1. Navigate to an area of the app that uses Flutter. The terminal message should now change from `Listening.` to `Synchronizing files to device`.

You can now edit the Flutter `.dart` code, and the changes can be hot reloaded by pressing `r` in the terminal.

# iOS

## Experiment: Integrate FlutterViewController
*The guide below is based on an early experiment to add Flutter to an existing iOS app. It contains a lot of
manual steps which we are working on making unnecessary.*

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
#include "./path/to/embedded/ios/Flutter/Generated.xcconfig"
```
An easy way to get the paths right to copy it from `embedded` (after running it with `flutter run`).

Now this configuration file must be included in your existing xcconfig file with the line:
```
#include "Flutter/FlutterConfig.xcconfig"
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

### Make your ApplicationDelegate a `FlutterAppLifeCycleProvider`
This will forward app lifecycle events to your plugins and keep the connection to the flutter tools open during pause. 

* Create a `FlutterPluginAppLifeCycleDelegate` and forward events to it. 
* And then implement `FlutterAppLifeCycleProvider` to get notified when plugins subscribe for events.

Here is the minimal code forwarding all events:

AppDelegate.h:
```
#import <UIKit/UIKit.h>
#import <Flutter/Flutter.h>

@interface AppDelegate : FlutterAppDelegate <UIApplicationDelegate, FlutterAppLifeCycleProvider>
@end
```

AppDelegate.m:
```
#import <Flutter/Flutter.h>

@interface AppDelegate ()

@end

@implementation AppDelegate

{
    FlutterPluginAppLifeCycleDelegate *_lifeCycleDelegate;
}
- (instancetype)init {
    if (self = [super init]) {
        _lifeCycleDelegate = [[FlutterPluginAppLifeCycleDelegate alloc] init];
    }
    return self;
}

- (BOOL)application:(UIApplication*)application
didFinishLaunchingWithOptions:(NSDictionary*)launchOptions {
    return [_lifeCycleDelegate application:application didFinishLaunchingWithOptions:launchOptions];
}

// Returns the key window's rootViewController, if it's a FlutterViewController.
// Otherwise, returns nil.
- (FlutterViewController*)rootFlutterViewController {
    UIViewController* viewController = [UIApplication sharedApplication].keyWindow.rootViewController;
    if ([viewController isKindOfClass:[FlutterViewController class]]) {
        return (FlutterViewController*)viewController;
    }
    return nil;
}

- (void)touchesBegan:(NSSet*)touches withEvent:(UIEvent*)event {
    [super touchesBegan:touches withEvent:event];
    
    // Pass status bar taps to key window Flutter rootViewController.
    if (self.rootFlutterViewController != nil) {
        [self.rootFlutterViewController handleStatusBarTouches:event];
    }
}

- (void)applicationDidEnterBackground:(UIApplication*)application {
    [_lifeCycleDelegate applicationDidEnterBackground:application];
}

- (void)applicationWillEnterForeground:(UIApplication*)application {
    [_lifeCycleDelegate applicationWillEnterForeground:application];
}

- (void)applicationWillResignActive:(UIApplication*)application {
    [_lifeCycleDelegate applicationWillResignActive:application];
}

- (void)applicationDidBecomeActive:(UIApplication*)application {
    [_lifeCycleDelegate applicationDidBecomeActive:application];
}

- (void)applicationWillTerminate:(UIApplication*)application {
    [_lifeCycleDelegate applicationWillTerminate:application];
}

- (void)application:(UIApplication*)application
didRegisterUserNotificationSettings:(UIUserNotificationSettings*)notificationSettings {
    [_lifeCycleDelegate application:application
didRegisterUserNotificationSettings:notificationSettings];
}

- (void)application:(UIApplication*)application
didRegisterForRemoteNotificationsWithDeviceToken:(NSData*)deviceToken {
    [_lifeCycleDelegate application:application
didRegisterForRemoteNotificationsWithDeviceToken:deviceToken];
}

- (void)application:(UIApplication*)application
didReceiveRemoteNotification:(NSDictionary*)userInfo
fetchCompletionHandler:(void (^)(UIBackgroundFetchResult result))completionHandler {
    [_lifeCycleDelegate application:application
       didReceiveRemoteNotification:userInfo
             fetchCompletionHandler:completionHandler];
}

- (BOOL)application:(UIApplication*)application
            openURL:(NSURL*)url
            options:(NSDictionary<UIApplicationOpenURLOptionsKey, id>*)options {
    return [_lifeCycleDelegate application:application openURL:url options:options];
}

- (BOOL)application:(UIApplication*)application handleOpenURL:(NSURL*)url {
    return [_lifeCycleDelegate application:application handleOpenURL:url];
}

- (BOOL)application:(UIApplication*)application
            openURL:(NSURL*)url
  sourceApplication:(NSString*)sourceApplication
         annotation:(id)annotation {
    return [_lifeCycleDelegate application:application
                                   openURL:url
                         sourceApplication:sourceApplication
                                annotation:annotation];
}

- (void)application:(UIApplication*)application
performActionForShortcutItem:(UIApplicationShortcutItem*)shortcutItem
  completionHandler:(void (^)(BOOL succeeded))completionHandler NS_AVAILABLE_IOS(9_0) {
    [_lifeCycleDelegate application:application
       performActionForShortcutItem:shortcutItem
                  completionHandler:completionHandler];
}

- (void)application:(UIApplication*)application
handleEventsForBackgroundURLSession:(nonnull NSString*)identifier
  completionHandler:(nonnull void (^)(void))completionHandler {
    [_lifeCycleDelegate application:application
handleEventsForBackgroundURLSession:identifier
                  completionHandler:completionHandler];
}

- (void)application:(UIApplication*)application
performFetchWithCompletionHandler:(void (^)(UIBackgroundFetchResult result))completionHandler {
    [_lifeCycleDelegate application:application performFetchWithCompletionHandler:completionHandler];
}

- (void)addApplicationLifeCycleDelegate:(NSObject<FlutterPlugin>*)delegate {
    [_lifeCycleDelegate addDelegate:delegate];
}

@end
```

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

[android-module-preview]: #preview-use-the-flutter-module-template
[android-experiment]: #experiment-turn-the-flutter-project-into-a-module
[ios-experiment]: #experiment-integrate-flutterviewcontroller
