Making it easy to add Flutter to an existing app is work in progress,
tracked by [#14821](https://github.com/flutter/flutter/issues/14821). This page
documents the current state of that work and will be updated as we build out the
necessary tooling.

Last updated August 23, 2018.

The "add2app" support is **in preview**, and is so far only available on the
master channel.

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
module project as a collection of pods.

### Make the host app depend on the Flutter module
The description below assumes that your existing iOS app has a structure similar
to what you get by asking Xcode version 9.4 to generate a new "Single View App"
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
#### Add a group for Flutter integration and setup configuration files
In Xcode, right-click the top-level MyApp project in the Project navigator and
select New Group and name it `Flutter`. Then right-click the `Flutter` group
and select New File. Select Configuration Settings File and press Next. Name it
`Flutter.xcconfig` and press Create.

Right-click the second-level MyApp group and create `Debug.xcconfig` and
`Release.xcconfig` in a similar way. Your host project structure should now
look as follows:
```
  MyApp/
    Flutter/
      Flutter.xcconfig
    MyApp/
      AppDelegate.h
      AppDelegate.m
      :
      Debug.xcconfig
      Release.xcconfig
```
Replace the contents of `Flutter.xcconfig` with lines to include
a configuration file from the Flutter module, and to disable bitcode
which is not supported by Flutter.

In `Flutter.xcconfig`:
```
#include "../../my_flutter/.ios/Flutter/Generated.xcconfig"
ENABLE_BITCODE=NO
```
Then include `Flutter.xcconfig` from both the Debug the Release configuration
files:

In `Debug.xcconfig`:
```
#include "../Flutter/Flutter.xcconfig"
```

In `Release.xcconfig`:
```
#include "../Flutter/Flutter.xcconfig"
FLUTTER_BUILD_MODE=release
```

Now we need to make the iOS project use the Debug and Release
configuration files: Select the top-level MyApp project in
the Project navigator. Select the PROJECT MyApp in the left
part of the main view, and then select the Info tab. In the
Configuration section, expand the Debug and Release items to
reveal the *second-level* MyApp targets. Then select Debug for
the former and Release for the latter.

#### Add a Flutter script build phase
Select the top-level MyApp project in the Project navigator.
Select the TARGET MyApp in the left part of the main view, and
then select the Build Phases tab. Add a new build phase by clicking
the + towards the top left of the main view. Select New Run Script
Phase. Expand the new Run Script, just appended to the list of phases.

Paste the following into the text area just below the Shell field:
```
"$FLUTTER_ROOT/packages/flutter_tools/bin/xcode_backend.sh" build
```
Finally, drag the new build phase to just after Target Dependencies.

#### Build the project and add Flutter artifacts to the host project
You should now be able to build the project. The `xcode_backend.sh`
script copies some Flutter artifacts into the Flutter folder, but they
won't be visible in the Xcode Flutter group before you add them to the
project: Right-click the Flutter group in the Project navigator, and
select Add Files to MyApp. Cmd-select both the `flutter_assets` folder
and the `engine/Flutter.framework`. Then click Add.

The two selected items should now appear in the Project navigator,
inside the Flutter group:
```
  MyApp/
    Flutter/
      flutter_assets
      Flutter.framework
      Flutter.xcconfig
    MyApp/
      AppDelegate.h
      AppDelegate.m
      :
      Debug.xcconfig
      Release.xcconfig
```
Now embed the Flutter framework in the MyApp target: Select the top-level
MyApp project in the Project navigator. Select the TARGET MyApp in the
left part of the main view and then select the General tab. In the Embedded
Binaries section, click the + and select `Flutter.framework` from the pop-up
dialog. Click Add.

### Write code to use FlutterViewController from your host app.
The proper place to do this will be specific to your host app. Here is an
example that makes sense for the blank screen of the host app generated
by Xcode 9.4.

Include Flutter at the top of `ViewController.m`:
```objective-c
#import "Flutter/Flutter.h"
```

In the `viewDidLoad` method of `ViewController.m`, add the following:
```objective-c
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
```
You should now be able to build and launch MyApp on the Simulator or on a device.
Pressing the button should bring up a full-screen Flutter view with the standard
Flutter Demo counting app.

### Plugins
If your Flutter module involves plugin dependencies, you have to use the CocoaPods
dependency manager for your host app. Please refer to
[cocoapods.org](https://cocoapods.org/) for how to install CocoaPods, if needed.

#### Setting up CocoaPods
In case your project doesn't already use CocoaPods, you need to give it a `Podfile`
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
If MyApp uses Swift you need to include the `use_frameworks!` directive:
```ruby
platform :ios, '9.0'

target 'MyApp' do
  use_frameworks!
end
```
Next you need to include CocoaPods configuration files:

In `some/path/MyApp/MyApp/Debug.xcconfig`, add:
```xcconfig
#include "Pods/Target Support Files/Pods-MyApp/Pods-MyApp.debug.xcconfig"
```

In `some/path/MyApp/MyApp/Release.xcconfig`, add:
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
Make sure you have executed `flutter packages get` from `some/path/my_flutter` so
that the Flutter module reflects the plugins declared in the `pubspec.yaml`.
Then run `pod install` from `some/path/MyApp` and you should see two Flutter pods being
installed plus one pod per plugin.

You can now open the MyApp project in Xcode again by directing Xcode to the
`some/path/MyApp/MyApp.xcworkspace` file, (**not** the `.xcodeproj` file!).

#### Convey application delegate calls to plugins
If your UIApplicationDelegate is not already a sub-class of another type, make it inherit from `FlutterAppDelegate`:

For the Single View project generated by Xcode, `AppDelegate.h` can be simply:
```objectivec
#import <Flutter/Flutter.h>
#import <UIKit/UIKit.h>

@interface AppDelegate : FlutterAppDelegate
@end
```

TBD: what to do if the app delegate already inherits from somewhere else.

#### Register plugins with each Flutter view
Finally, you need to ensure that plugins are registered with each Flutter view created.
The `ViewController.m` code listed earlier would change as follows:
```objectivec
:
#import "FlutterPluginRegistrant/GeneratedPluginRegistrant.h"
:
- (void)handleButtonAction {
  FlutterViewController* flutterViewController = [[FlutterViewController alloc] init];
  [GeneratedPluginRegistrant registerWithRegistry:flutterViewController]; // new
  [self presentViewController:flutterViewController animated:false completion:nil];
}
```

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