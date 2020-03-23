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
desktop support. The [flutter-desktop-embedding project's plugins](https://github.com/google/flutter-desktop-embedding/tree/master/plugins) are one source for desktop plugins.

Since the plugin APIs and tooling for Windows and Linux are not yet stable, publishing
Windows and/or Linux plugins to pub.dev is strongly discouraged, but they can be referenced manually. See
the link above for an example.

## Tooling

Support for desktop in the `flutter` tool is a work in progress. To use it, you must be on the `master` [Flutter channel](https://github.com/flutter/flutter/wiki/Flutter-build-release-channels), and you must enable the feature for your platform:
* `flutter config --enable-windows-desktop` to enable Windows.
* `flutter config --enable-linux-desktop` to enable Linux.

Run `flutter config` to see your current settings, as well as the commands to disable the feature again.

### `doctor`

Once you've enabled desktop support, running `flutter doctor` will check for the necessary tools to build for your desktop platform. Ensure that it reports no issues in that section before trying to `build` or `run`.

### `create`

Support for `create` (`app` and `plugin` templates only) currently varies by platform:
- macOS: Fully supported.
- Windows: Working, but not yet stabilized. After Flutter updates, you may need to delete and re-create the `windows` directory.
- Linux: Working, but not yet stabilized. After Flutter updates, you may need to delete and re-create the `linux` directory.

You can add desktop support to an existing app or plugin by running `flutter create .` in the project. (You may want to revert changes to the `ios` and `android` directories after re-running `create`)

**Note**: Windows and Linux plugin APIs and tooling are not yet stable, so any plugin written now will need frequent updates for breaking changes. Because of this, publishing Windows and/or Linux plugins to pub.dev at this stage is **strongly discouraged**.

When using `flutter create .` in an existing plugin to add desktop support, be sure to update [the `platforms` map in `pubspec.yaml`](https://flutter.dev/docs/development/packages-and-plugins/developing-packages#plugin-platforms).

### `run` and `build`

`flutter run` and `flutter build` are supported on all desktop platforms once you have added the necessary platform directory to your project (see `create` above). Breaking changes are still common on Windows and Linux however, so as noted above you should expect to need to re-create after any Flutter update to avoid build failures.

Only debug mode is currently supported for Windows and Linux.

### IDEs ###

If you have enabled desktop support in the tool and added desktop support to your project as described above, your machine should appear as an available device in Android Studio or VS Code for that project. The standard Flutter build and run workflows should then automatically work for desktop as well.

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