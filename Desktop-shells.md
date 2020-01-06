Work is ongoing to extend Flutter to support desktop as a target environment, allowing developers to create macOS, Windows, and Linux applications with Flutter.

## Current Status

A high-level overview of the status of each platform is provided below. For details see
[the list of desktop-related bugs](https://github.com/flutter/flutter/issues?utf8=%E2%9C%93&q=is%3Aissue+is%3Aopen+label%3A%22a%3A+desktop%22),
as well as [the embedding source](https://github.com/flutter/engine/tree/master/shell/platform/).

### macOS

macOS is the most developed of the desktop platforms, and has entered alpha. For macOS, see the
[flutter.dev documentation](https://flutter.dev/desktop), rather than this page, for instructions and
information.

### Windows

The Windows shell is an early technical preview. It is Win32-based, but we plan to explore UWP support in
the future.

The APIs for the final embedding may be significantly different from the current API surface.

### Linux

The current Linux shell is a GLFW placeholder, to allow early experimentation, and will be replaced with
a different implementation in the future. We would like to create a library 
that lets you embed Flutter regardless of whether you're using GTK+, Qt, wxWidgets, Motif, or another arbitrary
toolkit for other parts of your application, but have not yet determined a good way to do that. Our current plan is to support GTK+ out of the box, in a way where adding support for other toolkits is straightforward.

Expect the APIs for the final shell to be radically different from the current implementation.

### Plugins

Writing plugins is supported on all platforms, however there are currently few plugins that actually have
desktop support. The [flutter-desktop-embedding project's plugins](https://github.com/google/flutter-desktop-embedding/tree/master/plugins) are one source for Windows and Linux plugins.

Since the plugin APIs and tooling for Windows and Linux are not yet stable, publishing
Windows and/or Linux plugins to pub.dev is strongly discouraged, but they can be referenced manually. See
below for details.

## Tooling

Support for desktop in the `flutter` tool is a work in progress. To use it, you must be on the `master` [Flutter channel](https://github.com/flutter/flutter/wiki/Flutter-build-release-channels), and you must enable the feature for your platform:
* `flutter config --enable-windows-desktop` to enable Windows.
* `flutter config --enable-linux-desktop` to enable Linux.

Run `flutter config` to see your current settings, as well as the commands to disable the feature again.

### `create`

Currently, macOS is the only desktop platform supported by `flutter create`. For Windows and Linux, the [flutter-desktop-embedding project](https://github.com/google/flutter-desktop-embedding) has simple runners for each desktop platform that work with the `flutter` tool's in-progress desktop support, which you can add to your own project. See the READMEs there for details.

**Be sure to read the 'Flutter Application Requirements' section below!**

### `run` and `build`

`flutter run` and `flutter build` are supported on all desktop platforms once you have added the necessary platform directory to your project (see `create` above). Breaking changes are still common on Windows and Linux however, so you should expect to need to get the latest runners from flutter-desktop-embedding after any Flutter update.

Only debug mode is currently supported for Windows and Linux.

### IDEs ###

If you have enabled desktop support and added a desktop runner to your project, as described above, your desktop should appear as an available device in Android Studio or VS Code for that project. The standard Flutter build and run workflows should then automatically work for desktop as well.

### Plugins

Plugin tooling for Windows and Linux is not yet implemented. For now you must manually update your native build (`vcxproj`, `Makefile`) to build each plugin, include its header, register it, and link its shared library with the executable. See the [plugins section of the flutter-desktop-embedding project](https://github.com/google/flutter-desktop-embedding/tree/master/plugins) for an example plugin to use as a starting point for building your own, and for details on using plugins built from that example.

## Flutter Application Requirements

Because Windows and Linux are not yet fully supported by the Flutter framework, existing Flutter
applications are likely to require slight modifications to run.

### Target Platform Override

Most applications will need to override the target platform for the application
to one of the supported values in order to avoid 'Unknown platform' exceptions,
to work around the fact that [those platforms are not yet valid `TargetPlatform`
values](https://github.com/flutter/flutter/issues/31366).
This should be done as early as possible. For instance:

```dart
import 'package:flutter/foundation.dart'
    show debugDefaultTargetPlatformOverride;
[...]

/// If the current platform is a desktop platform that isn't yet supported by
/// TargetPlatform, override the default platform to one that is.
/// Otherwise, do nothing.
void _setTargetPlatformForDesktop() {
  // No need to handle macOS, as it has now been added to TargetPlatform.
  if (Platform.isLinux || Platform.isWindows) {
    debugDefaultTargetPlatformOverride = TargetPlatform.fuchsia;
  }
}

void main() {
  _setTargetPlatformForDesktop();
  [...]
}
```

Note that the choice of `fuchsia` here is arbitrary; you could use any supported
platform. However, the target platform you use will affect the behavior
and appearance of the widgets, as well expectations Flutter will have for what
is available on the platform, such as fonts.

### Fonts

Flutter applications may default to fonts that are standard for the target
platform, but unavailable on desktop. For instance, if the target platform is
`TargetPlatform.iOS` the Material library will default to San Francisco, which
is available on macOS but not Linux or Windows.

Most applications using the override above will need to set the font
(e.g., via `ThemeData`) based on the host platform, or set a specific font that
is bundled with the application. Other widgets that doesn't use `ThemeData` may
not display without extra font specification (e.g., the `DEBUG` banner's text).

Symptoms of missing fonts include text failing to display and/or console logging
about failure to load fonts, since font fallback is not yet robust on all desktop
platforms (see the [Windows](https://github.com/flutter/flutter/issues/39915) and
[Linux](https://github.com/flutter/flutter/issues/30700) issues for status).

### Plugins

If your project uses any plugins they won't work (unless they have desktop support),
as the native side will be missing. Depending on how the Dart side of the
plugin is written, they may fail gracefully, or may throw errors.

## Add-to-App

Currently there is no support for Add-to-App for any desktop platform (either via `flutter create -t module` or by flutter-desktop-embedding example). If you are familiar with doing native development on your platform(s), it is possible to integrate the desktop Flutter libraries in your own app. There is not currently much guidance, so you will be well off the beaten path, but the information below will help get you started.

### Getting the Libraries

Unless you want to build the Flutter engine from source, you will need a prebuilt library. The easiest way to get the right version for your version of Flutter is to run `flutter precache` with the `--linux`, `--macos`, or `--windows` flag (depending on your platform). They will be downloaded to `bin/cache/artifacts/engine/` under your Flutter tree.

Only `debug` libraries are currently available for Windows and Linux.

### C++ Wrapper

The Windows and Linux libraries provide a C API. To make it easier to use them, there is a C++ wrapper available
which you can build into your application to provide a higher-level API surface. The source for it is downloaded with the library. You will need to build it as part of your application.

### Documentation

See the headers that come with the library (or wrapper) for your platform for information on using them. More documentation will be available in the future; for now it may be helpful to look at the flutter-desktop-embedding example application to see how it uses them.

### Building

In addition to linking the Flutter library, your application will need to bundle your Flutter assets (as created by `flutter build bundle`). On Windows and Linux you will also need the ICU data from the Flutter engine
(look for `icudtl.dat` under the `bin/cache/artifacts/engine` directory in your Flutter tree).