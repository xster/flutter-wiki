Flutter is committed to delivering a fast developer workflow. The Flutter tools support near-instant reload of changes powered by the Dart VM's new _hot reload_ feature, both from the Flutter CLI tools and IntelliJ.

We'd love [feedback](https://github.com/flutter/flutter/issues/new) on this new feature and it's user experience. You can try hot reload now, if:

* You are running Linux or MacOS and are using an Android device or emulator for development.
* You are running MacOS and are using the iOS simulator for development.
* You are using the latest master branch of flutter.

## Using IntelliJ

You need the 'flutter' plugin, see [setup](https://flutter.io/setup/#flutter-intellij-ide-plugins).

The next time you debug your app, you will see a reload button in the debugger UI (in the bottom debug pane; the icon shows a lightning bolt and a play button).

## Using the CLI

Hot Reload is enabled by default when you run your app with `flutter run`. After making a change to the source of your Flutter app, return to the console that is running the `flutter run` command. Type `r` or `F5` to reload your app.

## Feedback

This feature is a work in progress, and we look forward to your feedback. Please let us know if you run into any issues, and if you enjoy and use the faster development workflow.

You can reach us at `flutter-dev@googlegroups.com` and our [Gitter chat](https://gitter.im/flutter/flutter).

## Supported changes

Currently the following kinds of code changes are hot reloadable:

* Classes
  * Adding a method/function
  * Removing a method/function
  * Class hierarchy edits
  * Adding a static or instance field
  * Removing a static or instance field
* Libraries
  * Adding a library import
  * Removing a library import
  * Adding a part
  * Removing a part
  * Adding a global variable
  * Removing a global variable
* Functions
  * Change the signature (number of arguments, etc) of function
  * Change the implementation of a function
* Enums
  * Adding a new enum value
  * Removing an enum value