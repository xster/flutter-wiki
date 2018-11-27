## Intro

Making it easy to add Flutter to an existing app is work in progress,
tracked by the [Add2App project](https://github.com/flutter/flutter/projects/28). 

This page documents the current state of that work and will be updated as we build out the
necessary tooling.

Last updated November 26, 2018.

The "add2app" support is **in preview**, and is so far only available on the
master channel.

## Disclaimer

Since Flutter's "Add2App" functionality is in preview, the associated APIs and
tooling are not stable and are subject to change.

## The Flutter module project template

Flutter projects created using `flutter create xxx` include very simple host
apps for your Flutter/Dart code (a single-Activity Android host and a
single-ViewController iOS host). You can modify these host apps to suit your
needs and build from there.

But if you're starting off with an *existing* host app for either platform,
you'll likely want to include your Flutter project in that app as some form of
library instead.

This is what the Flutter module template provides. Executing
`flutter create -t module xxx` produces a Flutter project containing an Android
library and a Cocoapods pod designed for consumption by your existing host app.

## Android

### Create a Flutter module
Let's assume you have an existing Android app at `some/path/MyApp`, and that you
want your Flutter project as a sibling:
```
$ cd some/path/
$ flutter create -t module my_flutter
```
This creates a `some/path/my_flutter/` Flutter module project with some Dart
code to get you started and a `.android/` hidden subfolder that wraps up the
module project in an Android library.

(While not required in what follows, if you so desire, you can build that library
using Gradle:
```
$ cd .android/
$ ./gradlew flutter:assembleDebug
```
This results in a `flutter-debug.aar` archive file in
`.android/Flutter/build/outputs/aar/`.)

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
The binding and script evaluation allows the Flutter module to `include` itself
(as `:flutter`) and any Flutter plugins used by the module (as `:package_info`,
`:video_player`, etc) in the evaluation context of your `settings.gradle`.

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
Use the Flutter module's Java API to add Flutter views to your host app. This
can be done by directly using `Flutter.createView`:
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
It is also possible to create a `FlutterFragment` that takes care of lifecycle
by itself:
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
Above we use the string `"route1"` to tell the Dart code which widget to display
in the Flutter view. The `lib/main.dart` file of the Flutter module project
template should `switch` on (or otherwise interpret) the provided route string,
available as `window.defaultRouteName`, to determine which widget to create and
pass to `runApp`. Schematically,
```dart
import 'dart:ui';
import 'package:flutter/material.dart';

void main() => runApp(_widgetForRoute(window.defaultRouteName));

Widget _widgetForRoute(String route) {
  switch (route) {
    case 'route1':
      return SomeWidget(...);
    case 'route2':
      return SomeOtherWidget(...);
    default:
      return Center(
        child: Text('Unknown route: $route', textDirection: TextDirection.ltr),
      );
  }
}
```
It is entirely up to you which route strings you want and how to interpret them.

### Building and running your app
You build and run `MyApp` in exactly the same way that you did before you added
the Flutter module dependency, typically using Android Studio. The same goes for
editing, debugging, and profiling your Android code.

### Hot restart/reload and debugging Dart code
Full IDE integration to support working with the Flutter/Dart code of your
hybrid app is work in progress. But the fundamentals are already present via the
Flutter command line tools and the Dart Observatory web user interface.

Connect a device or launch an emulator. Then make Flutter CLI tooling listen
for your app to come up:
```
$ cd some/path/my_flutter
$ flutter attach
Waiting for a connection from Flutter on Nexus 5X...
```
Launch `MyApp` in debug mode from Android Studio (or whichever way you usually
do it). Navigate to an area of the app that uses Flutter. Then turn back to the
terminal, and you should see output similar to the following:
```
Done.
Syncing files to device Nexus 5X...                          5.1s

🔥  To hot reload changes while running, press "r". To hot restart (and rebuild state), press "R".
An Observatory debugger and profiler on Nexus 5X is available at: http://127.0.0.1:59556/
For a more detailed help message, press "h". To quit, press "q".
```
You can now edit the Dart code in `my_flutter`, and the changes can be hot
reloaded by pressing `r` in the terminal. You can also paste the URL above into
your browser to use the Dart Observatory for setting breakpoints, analyzing
memory retention and other debugging tasks.

## iOS

### Create a Flutter module
Let's assume you have an existing iOS app at `some/path/MyApp`, and that you
want your Flutter project as a sibling:
```
$ cd some/path/
$ flutter create -t module my_flutter
```
This creates a `some/path/my_flutter/` Flutter module project with some Dart
code to get you started and a `.ios/` hidden subfolder that wraps up the
module project that contains some Cocoapods and a helper Ruby script.

### Make the host app depend on the Flutter module
The description below assumes that your existing iOS app has a structure similar
to what you get by asking Xcode version 10.0 to generate a new "Single View App"
project using Objective-C. If your existing app has a different folder structure
and/or existing `.xcconfig` files, you can reuse those, but probably need to adjust
some of the relative paths mentioned below accordingly.

The assumed folder structure is as follows:
```
some/path/
  my_flutter/
    lib/main.dart
    .ios/
  MyApp/
    MyApp/
      AppDelegate.h
      AppDelegate.m (or swift)
      :
```

#### Add your Flutter app to your Podfile
Integrating the Flutter framework requires use of the CocoaPods dependency manager.
This is because the Flutter framework needs to be available also to any Flutter plugins
that you might include in my_flutter.

Please refer to [cocoapods.org](https://cocoapods.org/) for how to install CocoaPods
on your development machine, if needed.

If your host application (`MyApp`) is already using Cocoapods, you only have to do the
following to integrate with your `my_flutter` app:

1. Add the following lines to your `Podfile`:
```ruby
  flutter_application_path = 'path/to/flutter_app/'
  eval(File.read(File.join(flutter_application_path, '.ios', 'Flutter', 'podhelper.rb')), binding)
```
1. Run `pod install`.

Whenever you change the Flutter plugin dependencies in `some/path/my_flutter/pubspec.yaml`,
you need to run `flutter packages get` from `some/path/my_flutter` to refresh the list
of plugins read by the `podhelper.rb` script. Then run `pod install` again from
`some/path/MyApp`.


The `podhelper.rb` script will ensure that your plugins and the Flutter.framework
get added to your project, and also ensure that bitcode is disabled for all targets.

#### Add a build phase for building the Dart code
Select the top-level `MyApp` project in the Project navigator.
Select __TARGET__ `MyApp` in the left part of the main view, and
then select the `Build Phases` tab. Add a new build phase by clicking
the `+` towards the top left of the main view. Select `New Run Script Phase`. 
Expand the new `Run Script`, just appended to the list of phases.

Paste the following into the text area just below the Shell field:
```sh
"$FLUTTER_ROOT/packages/flutter_tools/bin/xcode_backend.sh" build
"$FLUTTER_ROOT/packages/flutter_tools/bin/xcode_backend.sh" embed
```

Finally, drag the new build phase to just after the Target Dependencies
phase.

You should now be able to build the project using `⌘B`. 

#### Under the hood

If you have some reason to do this manually or debug why these steps aren't working, here's what's going on under the hood:

1. `Flutter.framework` (the Engine library) is getting embedded into your app for you.  This has to match up with the release type (debug/profile/release) as well as the architecture for your app (arm*, i386, x86_64, etc.).  Cocoapods pulls this in as a vendored framework and makes sure it gets embedded into your native app.
2. `App.framework` (your Flutter application binary) is embedded into your app.
3. `flutter_assets` folder is getting embedded as a resource - it contains fonts, images, and in certain build modes it also contains binary files required by the engine at runtime.  __Problems with this folder can lead to runtime errors such as "Could not run engine for configuration" - usually indicating that either the folder is not getting embedded, or you're trying to cross a JIT application with an AOT enabled engine, or vice versa!__
4. Any plugins are getting added as Cocoapods.  In theory, it should be possible to manually merge those in as well, but that becomes much more specific to the plugin itself.
5. Bitcode is disabled for every target in your project.  This is a requirement to link with the Flutter Engine.
6. Generated.xcconfig (containing Flutter-specific environment varaibles) is included in the release and debug .xcconfig files that Cocoapods generates.

The build phase script (xcode_backend.sh) is ensuring that the binaries you build stay up to date with the Dart code that's actually in the folder.  It also attempt to respect your build configuration setting once [this pull request](https://github.com/flutter/flutter/pull/23387) lands.

### Write code to use FlutterViewController from your host app
The proper place to do this will be specific to your host app. Here is an
example that makes sense for the blank screen of the host app generated
by Xcode 10.0.

First declare your app delegate to be a subclass of `FlutterAppDelegate`.

In `AppDelegate.h`:
```objective-c
#import <UIKit/UIKit.h>
#import <Flutter/Flutter.h>

@interface AppDelegate : FlutterAppDelegate
@end
```

This allows `AppDelegate.m` to be really simple, unless your host app
needs to override other methods here:
```objective-c
#import <FlutterPluginRegistrant/GeneratedPluginRegistrant.h> // Only if you have Flutter Plugins

#include "AppDelegate.h"

@implementation AppDelegate

// This override can be omitted if you do not have any Flutter Plugins.
- (BOOL)application:(UIApplication *)application
    didFinishLaunchingWithOptions:(NSDictionary *)launchOptions {
  [GeneratedPluginRegistrant registerWithRegistry:self];
  return [super application:application didFinishLaunchingWithOptions:launchOptions];
}

@end
```

If you are writing in Swift, you can do the following in your `AppDelegate.swift`:
```swift
import UIKit
import Flutter
import FlutterPluginRegistrant // Only if you have Flutter Plugins.

@UIApplicationMain
class AppDelegate: FlutterAppDelegate {

  // Only if you have Flutter plugins.
  override func application(_ application: UIApplication, didFinishLaunchingWithOptions launchOptions: [UIApplicationLaunchOptionsKey: Any]?) -> Bool {
    GeneratedPluginRegistrant.register(with: self);
    return super.application(application, didFinishLaunchingWithOptions: launchOptions);
  }

}
```

<details>
<summary>What to do if the app delegate already inherits from somewhere else.</summary>

Make your app delegate implement the `FlutterAppLifeCycleProvider` protocol, e.g.:
```objective-c
#import <Flutter/Flutter.h>
#import <UIKit/UIKit.h>
#import <FlutterPluginRegistrant/GeneratedPluginRegistrant.h> // Only if you have Flutter Plugins

@interface AppDelegate : UIResponder <UIApplicationDelegate, FlutterAppLifeCycleProvider>
@property (strong, nonatomic) UIWindow *window;
@end
```

The implementation should mostly just delegate to a `FlutterPluginAppLifeCycleDelegate`:
```objective-c
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
    [GeneratedPluginRegistrant registerWithRegistry:self]; // Only if you are using Flutter plugins.
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
</details>

`ViewController.m`:
```objective-c
#import <Flutter/Flutter.h>
#import "ViewController.h"

@implementation ViewController
- (void)viewDidLoad {
    [super viewDidLoad];
    UIButton *button = [UIButton buttonWithType:UIButtonTypeCustom];
    [button addTarget:self
               action:@selector(handleButtonAction)
     forControlEvents:UIControlEventTouchUpInside];
    [button setTitle:@"Press me" forState:UIControlStateNormal];
    [button setBackgroundColor:[UIColor blueColor]];
    button.frame = CGRectMake(80.0, 210.0, 160.0, 40.0);
    [self.view addSubview:button];
}

- (void)handleButtonAction {
    FlutterViewController* flutterViewController = [[FlutterViewController alloc] init];
    [self presentViewController:flutterViewController animated:false completion:nil];
}
@end
```

Or, using Swift:

`ViewController.swift`:
```swift
import UIKit
import Flutter

class ViewController: UIViewController {
  override func viewDidLoad() {
    super.viewDidLoad()
    let button = UIButton(type:UIButtonType.custom)
    button.addTarget(self, action: #selector(handleButtonAction), for: .touchUpInside)
    button.setTitle("Press me", for: UIControlState.normal)
    button.frame = CGRect(x: 80.0, y: 210.0, width: 160.0, height: 40.0)
    button.backgroundColor = UIColor.blue
    self.view.addSubview(button)
  }

  @objc func handleButtonAction() {
    let flutterViewController = FlutterViewController()
    self.present(flutterViewController, animated: false, completion: nil)
  }
}
```

You should now be able to build and launch MyApp on the Simulator or on a device.
Pressing the button should bring up a full-screen Flutter view with the standard
Flutter Demo counting app. You can use routes to show different widgets at different
places in your app, as described in the Android section above. To set the route, call

- Objective-C:
```objective-c
[flutterViewController setInitialRoute:@"route1"];
```

- Swift: 
```swift
flutterViewController.setInitialRoute("route1")
```

immediately after construction of the `FlutterViewController` (and before presenting
it).

You can have the Flutter app dismiss itself by calling `SystemNavigator.pop()`
in the Dart code.

### Building and running your app
You build and run MyApp using Xcode in exactly the same way that you did before you
added the Flutter module dependency. The same goes for editing, debugging, and
profiling your iOS code.

### Hot restart/reload and debugging Dart code
Connect a device or launch a Simulator. Then make Flutter CLI tooling listen
for your app to come up:
```
$ cd some/path/my_flutter
$ flutter attach
Waiting for a connection from Flutter on iPhone X...
```
Launch `MyApp` in debug mode from Xcode. Navigate to an area of the app that uses
Flutter. Then turn back to the terminal, and you should see output similar to the
following:
```
Done.
Syncing files to device iPhone X...                          4.7s

🔥  To hot reload changes while running, press "r". To hot restart (and rebuild state), press "R".
An Observatory debugger and profiler on iPhone X is available at: http://127.0.0.1:54741/
For a more detailed help message, press "h". To quit, press "q".
```
You can now edit the Dart code in `my_flutter`, and the changes can be hot
reloaded by pressing `r` in the terminal. You can also paste the URL above into
your browser to use the Dart Observatory for setting breakpoints, analyzing
memory retention and other debugging tasks.

#### Debugging specific instances of Flutter

It's possible to add multiple instances of Flutter (`root isolates`) to an app. `flutter attach` connects to all of the available isolates by default. Any commands sent from the attached CLI are then forwarded to each of the attached isolates.

List all attached isolates by typing `l` from an attached `flutter` CLI tool. If unspecified, isolate names are automatically generated from the dart entry point file and function name.

Example `l` output for an application that's displaying two Flutter isolates simultaneously:

```
Connected views:
  main.dart$main-517591213 (isolates/517591213)
  main.dart$main-332962855 (isolates/332962855)
```

Attach to specific isolates instead in two steps:

1. Name the Flutter root isolate of interest in its Dart source.
```dart
// main.dart
import 'dart:ui' as ui;

void main() {
  ui.window.setIsolateDebugName("debug isolate");
  // ...
}
```

2. Run `flutter attach` with the `--isolate-filter` option.
```
$ flutter attach --isolate-filter='debug'
Waiting for a connection from Flutter...
Done.
Syncing files to device...      1.1s

🔥  To hot reload changes while running, press "r". To hot restart (and rebuild state), press "R".
An Observatory debugger and profiler is available at: http://127.0.0.1:43343/
For a more detailed help message, press "h". To detach, press "d"; to quit, press "q".

Connected view:
  debug isolate (isolates/642101161)
```

You can check out [`93573de`](https://github.com/flutter/flutter/commit/93573de2165c750fdeefcd2d620e2b8bd494fed6) for a more detailed example.