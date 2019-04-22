Work is ongoing to extend Flutter to support desktop as a target environment, allowing developers to create macOS, Windows, and Linux applications with Flutter. On the long run, this effort will create lead to a fully integrated solution where `flutter create`, `flutter run`, and `flutter build` work for desktop platforms as they do for mobile platforms, but currently this effort is still under way.

## Current Status

A high-level overview of the status of each platform is provided below. For details, see
[the source](https://github.com/flutter/engine/tree/master/shell/platform/).

**IMPORTANT:** The Flutter desktop APIs are still in early stages of development, and are **subject to change
without warning**. No backwards compatibility, either API or ABI, will be provided. Expect
any code using these libraries to need to be updated and recompiled after any Flutter update.

### macOS

This is the most mature of the desktop platforms (for various reasons, including that it's quite close to iOS, which we already support).

Classes starting with `Flutter` are shared with iOS, and should be essentially stable. Classes starting with
`FLE` are still in early stages.

### Windows

The current Windows shell is a GLFW placeholder, to allow early experimentation. It will be replaced in the
future with a Win32 or UWP shell that allows view-level embedding of Flutter within an application.

Expect the APIs for the final shell to be radically different from the current implementation.

### Linux

The current Linux shell is a GLFW placeholder, to allow early experimentation. We would like to create a library 
that lets you embed Flutter regardless of whether you're using GTK+, Qt, wxWidgets, Motif, or another arbitrary
toolkit for other parts of your application, but have not yet determined a good way to do that.

Expect the APIs for the final shell to be radically different from the current implementation.

### Plugins

Writing plugins is supported on all platforms, however, there are currently very few plugins that actually have
desktop support ([such as these plugins from the flutter-desktop-embedding
project](https://github.com/google/flutter-desktop-embedding/tree/master/plugins)).

For Windows and Linux, only the JSON codec is supported at the moment (which means the Dart side of most
existing plugins would need to be changed to implement desktop support). The standard codec should be coming
soon.

### Tooling

Support for desktop in the `flutter` tool is a work in progress. To use any of the support (such the host machine being listed by `flutter devices`), two things must currently be true:
- You must not be on the `stable` [Flutter channel](https://github.com/flutter/flutter/wiki/Flutter-build-release-channels). This is to make it clear that desktop support is not yet considered stable and production-ready.
- You must set the `ENABLE_FLUTTER_DESKTOP` environment variable to `true`. This is to avoid interfering with existing mobile development workflows while the long-term solution is being worked out (see [#30724](https://github.com/flutter/flutter/issues/30724)).

## Prebuilt Shell Libraries

The desktop libraries are not yet available in the Flutter SDK, but prebuilt versions are available for download. You will need the Flutter engine hash from the
[`engine.version`](https://github.com/flutter/flutter/blob/master/bin/internal/engine.version) file
in your Flutter framework checkout to download the correct version for your Flutter checkout. (If you use
a mismatched version, your Flutter application will likely fail to start.) Substitute that hash in the URLs
below in place of _`ENGINE_HASH`_

* macOS: `https://storage.googleapis.com/flutter_infra/flutter/ENGINE_HASH/darwin-x64/FlutterMacOS.framework.zip`
* Windows: `https://storage.googleapis.com/flutter_infra/flutter/ENGINE_HASH/windows-x64/windows-x64-flutter.zip`
* Linux: `https://storage.googleapis.com/flutter_infra/flutter/ENGINE_HASH/linux-x64/linux-x64-flutter.zip`

### C++ Wrapper

The Windows and Linux libraries provide a C API. To make it easier to use them, there is a C++ wrapper available
which you can build into your application to provide a higher-level API surface:
* Windows: `https://storage.googleapis.com/flutter_infra/flutter/ENGINE_HASH/windows-x64/flutter-cpp-client-wrapper.zip`
* Linux: `https://storage.googleapis.com/flutter_infra/flutter/ENGINE_HASH/linux-x64/flutter-cpp-client-wrapper.zip`

## Using the Shells

Since there is currently no tooling support for desktop shells, you will need to write a runner application
yourself, and link in the library, as well as any plugins you are using. This will require some familiarity
with doing native development on your platform(s); if you don't already have experience with that, you'll
probably want to check back later, when `flutter` tool support for desktop is available.

See the headers that come with the library for your platform for information on using them. More documentation
will be available in the future; for now it may be helpful to look at [the flutter-desktop-embedding
example](https://github.com/google/flutter-desktop-embedding/tree/master/example).

In addition to the Flutter library, your application will need to bundle your Flutter assets (as created by
`flutter build bundle`). On Windows and Linux you will also nee the ICU data from the Flutter engine
(look for `icudtl.dat` under the `bin/cache/artifacts/engine` directory in your Flutter tree).

#### macOS Note

Currently you must set up your FLEView in a XIB, rather than in code (this will change in the future). To
do so:
* Drag in an OpenGL View.
* Change the class to FLEView.
* Check the Double Buffer option. If your view doesn't draw, you have likely forgotten this step.
* Check the Supports Hi-Res Backing option. If you only see a portion of your application when running on
  a high-DPI monitor, you have likely forgotten this step.

### Plugins

#### macOS
When you set up your FLEViewController, before calling `launchEngine...`,
call `-registerWithRegistrar:` on each plugin you want to use. For
instance:

```objc
  [XYZMyAwesomePlugin registerWithRegistrar:
      [myFlutterViewController registrarForPlugin:"XYZMyAwesomePlugin"]];
```

#### Windows/Linux

After creating your Flutter window controller, call your plugin's registrar
function. For instance:

```cpp
  MyAwesomePluginRegisterWithRegistrar(
      flutter_controller.GetRegistrarForPlugin("MyAwesomePlugin"));
```

## Flutter Application Requirements

Because desktop platforms are not yet fully supported by the Flutter framework, existing Flutter
applications are likely to require slight modifications to run.

### Target Platform Override

Most applications will need to override the target platform for the application
to one of the supported values in order to avoid 'Unknown platform' exceptions.
This should be done as early as possible.

In the simplest case, where the code will only run on desktop and the behavior
should be consistent on all platforms, you can hard-code a single target:

```dart
import 'package:flutter/foundation.dart'
    show debugDefaultTargetPlatformOverride;
[...]

void main() {
  debugDefaultTargetPlatformOverride = TargetPlatform.fuchsia;
  [...]
}
```

If the code needs to run on both mobile and desktop, or you want different
behavior on different desktop platforms, you can conditionalize on `Platform`.
For example, the line in `main()` above could be replaced with a call to:

```dart
/// If the current platform is desktop, override the default platform to
/// a supported platform (iOS for macOS, Android for Linux and Windows).
/// Otherwise, do nothing.
void _setTargetPlatformForDesktop() {
  TargetPlatform targetPlatform;
  if (Platform.isMacOS) {
    targetPlatform = TargetPlatform.iOS;
  } else if (Platform.isLinux || Platform.isWindows) {
    targetPlatform = TargetPlatform.android;
  }
  if (targetPlatform != null) {
    debugDefaultTargetPlatformOverride = targetPlatform;
  }
}
```

Note that the target platform you use will affect not only the behavior and
appearance of the widgets, but also the expectations Flutter will have for
what is available on the platform, such as fonts.

### Fonts

Flutter applications may default to fonts that are standard for the target
platform, but unavailable on desktop. For instance, if the target platform is
`TargetPlatform.iOS` the Material library will default to San Francisco, which
is available on macOS but not Linux or Windows.

Most applications will need to set the font (e.g., via `ThemeData`) based
on the host platform, or set a specific font that is bundled with the
application.

Symptoms of missing fonts include text failing to display and console logging
about failure to load fonts.

### Plugins

If your project uses any plugins (unless they have desktop support), they won't
work, as the native side will be missing. Depending on how the Dart side of the
plugin is written, they may fail gracefully, or may throw errors.