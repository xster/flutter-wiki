Making it easy to add Flutter to an existing app is work in progress,
tracked by [#14821](https://github.com/flutter/flutter/issues/14821). This page
documents the current state of that work and will be updated as we build out the
necessary tooling.

Last updated October 5, 2018.

The "add2app" support is **in preview**, and is so far **only available on the
master channel**.

## The Flutter module project template

Previously Flutter projects created using `flutter create xxx` included very simple host
apps for your Flutter/Dart code (a single-Activity Android host and a
single-ViewController iOS host). You could modify these host apps to suit your
needs and build from there.

But if you're starting off with an *existing* host app for either platform,
you'll likely want to include your Flutter project in that app as some form of
library instead.

This is what the Flutter module provides. Executing
`flutter create xxx` from the master channel after October 4th 2018
produces a Flutter project containing an Android
library and a Cocoapods pod designed for consumption by your existing host app.

## Android

### Create a Flutter module
Let's assume you have an existing Android app at `some/path/MyApp`, and that you
want your Flutter project as a sibling:
```
$ cd some/path/
$ flutter create my_flutter
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
$ flutter create my_flutter
```
This creates a `some/path/my_flutter/` Flutter module project with some Dart
code to get you started and a `.ios/` hidden subfolder that wraps up the
module project as a collection of pods.

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
      AppDelegate.m
      :
```

#### Add groups for configuration and Flutter integration
In Xcode, right-click the top-level MyApp project in the Project navigator and
select New Group. Name the new group `Config`. Then add a `Flutter` group next
to it.

Right-click the Config group and invoke the New File... dialog. In the iOS/Other
section of the dialog, select Configuration Settings File and press Next. Name
the new file `Debug` and press Create. The .xcconfig file extension is added
automatically.

Repeat these to add a `Release` configuration file to the Config group and a
`Flutter` configuration file to the Flutter group.

Your host project structure should now look as follows:
```
  MyApp/
    Config/
      Debug.xcconfig
      Release.xcconfig
    Flutter/
      Flutter.xcconfig
    MyApp/
      AppDelegate.h
      AppDelegate.m
      :
```
Replace the contents of Flutter.xcconfig with lines to include an autogenerated
configuration file from the Flutter module, and to disable bitcode which is not
supported by Flutter.

In Flutter.xcconfig:
```xcconfig
#include "../../my_flutter/.ios/Flutter/Generated.xcconfig"
ENABLE_BITCODE=NO
```
Then include Flutter.xcconfig from both the Debug and the Release configuration
files:

In Debug.xcconfig:
```xcconfig
#include "../Flutter/Flutter.xcconfig"
```

In Release.xcconfig:
```xcconfig
#include "../Flutter/Flutter.xcconfig"
FLUTTER_BUILD_MODE=release
```

Now we need to make the iOS project use the Debug and Release
configuration files: Select the top-level MyApp project in
the Project navigator. Select PROJECT MyApp in the left
part of the main view, and then select the Info tab. In the
Configurations section, expand the Debug and Release items to
reveal the *second-level* MyApp targets. Then select Debug for
the former and Release for the latter.

#### Add a build phase for building the Dart code
Select the top-level MyApp project in the Project navigator.
Select TARGET MyApp in the left part of the main view, and
then select the Build Phases tab. Add a new build phase by clicking
the + towards the top left of the main view. Select New Run Script
Phase. Expand the new Run Script, just appended to the list of phases.

Paste the following into the text area just below the Shell field:
```
"$FLUTTER_ROOT/packages/flutter_tools/bin/xcode_backend.sh" build
```
Finally, drag the new build phase to just after the Target Dependencies
phase.

#### Build the project and add Flutter artifacts to the host project
You should now be able to build the project using ⌘B. This causes the
xcode_backend.sh script to create some Flutter artifacts in the
some/path/my_flutter/.ios/Flutter folder. We want to include two of them
in the Flutter group of the Xcode project: Right-click the Flutter group
in the Project navigator, and invoke the Add Files to "MyApp"... dialog.

Press ⌘+Shift G to navigate to the `some/path/my_flutter/.ios/Flutter`
folder. Then select `flutter_assets` in that folder and press Add. This
makes your Flutter assets part of the host app project.

Repeat the step above to add also `App.framework` to the Flutter group.
This makes your built Flutter Dart code part of the host app project.

The two selected items should now appear in the Project navigator,
inside the Flutter group:
```
  MyApp/
    Config/
      Debug.xcconfig
      Release.xcconfig
    Flutter/
      Flutter.xcconfig
      flutter_assets
      App.framework
    MyApp/
      AppDelegate.h
      AppDelegate.m
      :
```
Now embed the App.framework in the MyApp target: Select the top-level
MyApp project in the Project navigator. Select the TARGET MyApp in the
left part of the main view and then select the General tab. In the Embedded
Binaries section, press + and select `App.framework` from the pop-up
dialog. Press Add.

App.framework now appears twice in the Linked Frameworks and Libraries below.
Remove one occurrence.

### Make the host app depend on the Flutter framework

Integrating the Flutter framework requires use of the CocoaPods dependency manager.
This is because the Flutter framework needs to be available also to any Flutter plugins
that you might include in my_flutter.

Please refer to [cocoapods.org](https://cocoapods.org/) for how to install CocoaPods
on your development machine, if needed.

#### Setting up CocoaPods
In case your host project doesn't already use CocoaPods, you need to give it a `Podfile`
and let the `pod` tool do its magic. Close the MyApp project in Xcode, if open. Then
```
$ cd some/path/MyApp
$ pod init
$ pod install
```
A few warnings are printed to the console. You can silence those using a basic
text editor, as follows:

In the newly generated `some/path/MyApp/Podfile`, uncomment the line that declares
your target platform, e.g.:
```ruby
platform :ios, '9.0'

target 'MyApp' do
end
```
If MyApp uses Swift you need to add the `use_frameworks!` directive:
```ruby
platform :ios, '9.0'

target 'MyApp' do
  use_frameworks!
end
```
Next, include CocoaPods configuration files in your configuration files. Use a text
editor, not Xcode:

In `some/path/MyApp/Config/Debug.xcconfig`, add:
```xcconfig
#include "../Pods/Target Support Files/Pods-MyApp/Pods-MyApp.debug.xcconfig"
```

In `some/path/MyApp/Config/Release.xcconfig`, add:
```xcconfig
#include "Pods/Target Support Files/Pods-MyApp/Pods-MyApp.release.xcconfig"
```

At this point, another `pod install` invocation from `some/path/MyApp` should give
just a single warning saying that you have no dependencies yet.

#### Allow the Flutter module to inject pod dependencies
In `some/path/MyApp/Podfile`, add these two lines to the `target 'MyApp' do .. end`
construct:
```ruby
target 'MyApp' do
  flutter_application_path = '../my_flutter'
  eval(File.read("#{flutter_application_path}/.ios/Flutter/podhelper.rb"))
  ...
end
```
Run `pod install` from `some/path/MyApp` again and you should see two Flutter
framework pods being installed plus one pod per Flutter plugin used by my_flutter.

Whenever you change the Flutter plugin dependencies in `some/path/my_flutter/pubspec.yaml`,
you need to run `flutter packages get` from `some/path/my_flutter` to refresh the list
of plugins read by the podhelper.rb script. Then run `pod install` again from
`some/path/MyApp`.

You can now open the MyApp project in Xcode again by directing Xcode to the
`some/path/MyApp/MyApp.xcworkspace` file, (**not** the `.xcodeproj` file!).

Your project layout in Xcode now looks as follows:
```
  MyApp/
    Config/
      Debug.xcconfig
      Release.xcconfig
    Flutter/
      Flutter.xcconfig
      flutter_assets
      App.framework
    MyApp/
      AppDelegate.h
      AppDelegate.m
      :
    Products/
    Pods/
    Frameworks/
  Pods/
```

### Write code to use FlutterViewController from your host app
The proper place to do this will be specific to your host app. Here is an
example that makes sense for the blank screen of the host app generated
by Xcode 10.0.

First declare your app delegate to be a subclass of `FlutterAppDelegate`.

In AppDelegate.h:
```objective-c
#import <UIKit/UIKit.h>
#import <Flutter/Flutter.h>

@interface AppDelegate : FlutterAppDelegate
@end
```

This allows AppDelegate.m to be really simple, unless your host app
needs to override methods here:
```objective-c
#import "AppDelegate.h"

@implementation AppDelegate
@end
```

<details>
<summary>What to do if the app delegate already inherits from somewhere else.</summary>

Make your app delegate implement the `FlutterAppLifeCycleProvider` protocol, e.g.:
```objective-c
#import <Flutter/Flutter.h>
#import <UIKit/UIKit.h>

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

ViewController.m:
```objective-c
#import "ViewController.h"
#import "Flutter/Flutter.h"
#import "FlutterPluginRegistrant/GeneratedPluginRegistrant.h"

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
    [GeneratedPluginRegistrant registerWithRegistry:flutterViewController];
    [self presentViewController:flutterViewController animated:false completion:nil];
}
@end
```
You should now be able to build and launch MyApp on the Simulator or on a device.
Pressing the button should bring up a full-screen Flutter view with the standard
Flutter Demo counting app. You can use routes to show different widgets at different
places in your app, as described in the Android section above. To set the route, call
```objective-c
[viewController setInitialRoute:@"route1"];
```
immediately after construction of the `FlutterViewController`.

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