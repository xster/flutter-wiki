*Warning: This is early access stuff. Expect bumps along the way.*

Flutter is committed to delivering a fast developer workflow. The Flutter tools are currently being updated to bring the Dart VM's new _hot reload_ feature to the Flutter developer (both from the Flutter CLI tools and Atom).

We'd love [feedback](https://github.com/flutter/flutter/issues/new) on this new feature and UX. You can try hot reload now, if:

* You are running Linux or MacOS and are using an Android device or emulator for development.
* You are running MacOS and are using the iOS simulator for development.
* You are using the latest master branch of flutter.

Hot reload currently supports the iOS simulator, iOS hardware, and Android hardware, and Android emulator.

## Using Atom

You need to enable hot reload support by setting a flag. In Atom's settings for the Flutter plugin, toggle the `Enable hot reload` option.

The next time you run your app, you will see a reload button in the debugger UI (next to the stepping and terminate buttons). In addition, re-running your app (cmd-R) will issue a hot-reload.

## Using the CLI

You need to use the command-line option `--hot`. Run your app with `flutter run --hot`.

Then, make a change to the source of your Flutter app. (Not every change can be hot reloaded. See Caveats below.) Go to the console that is running the `flutter run --hot` command. Type `r` or `F5` to reload your app.

## Feedback

This feature is a work in progress, and we look forward to your feedback. Please let us know if you run into any issues, and if you enjoy and use the faster development workflow.

You can reach us at `flutter-dev@googlegroups.com` and our [Gitter chat](https://gitter.im/flutter/flutter).

## Caveats

Hot reload is a work in progress. The following kinds of code changes are hot reloadable:

* Classes
  * Adding a method/function
  * Removing a method/function
  * Class hierarchy edits
  * Adding a static field
  * Removing a static field
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


The following kinds of code and changes are not (currently) reloadable:

* Changing a const list or map.
* Changing a top-level field (after it has been initialized).
* Adding a field to a class.
* Removing a field from a class.

If you make a change from the list above, you need to restart your app to see that change.

## Roadmap

* Support for iOS hardware.
* Support for Android emulators.
* More kinds of code and changes.