*Warning: This is early access stuff. Expect bumps along the way.*

Flutter is committed to delivering a fast developer workflow. The Flutter tools are currently being updated to bring the Dart VM's new _hot reload_ feature to the Flutter developer (both from the Flutter CLI tools and Atom).

We'd love [feedback](https://github.com/flutter/flutter/issues/new) on this new feature and UX. You can try hot reload now, if:

* You have a Mac.
* You have XCode and the iOS simulator installed.
* You are using the latest master branch of flutter.

## Using Atom

You need to enable hot reload support by setting a flag. In Atom's config.cson file, under the 'flutter' section, add `reloadSources: true`.

The next time you run your app, you will see a reload button in the debugger UI, next to the stepping and terminate buttons.

## Using the CLI

You need to use a hidden command-line option. Run your app with `flutter run --reload-sources`.

Then, make a change to the source of your Flutter app. (Not every change can be hot reloaded. See Caveats below.) Go to the console that is running the `flutter run --reload-sources` command. Type `r` or `F5` to reload your app.

## Feedback

This feature is a work in progress, and we look forward to your feedback. Please let us know if you run into any issues, and if you enjoy and use the faster development workflow.

You can reach us at `flutter-dev@googlegroups.com` and our [Gitter chat](https://gitter.im/flutter/flutter).

## Caveats

Hot reload is a work in progress. The following kinds of code changes are hot reloadable:

* Changes to code inside functions or methods.
* Creation of new classes.
* Additions of functions or methods.

The following kinds of code and changes are not (currently) reloadable:

* Changing a const list or map.
* Changing a top-level `final` field (after it has been initialized).
* Added a field to a class.
* Removing a field from a class.

If you make a change from the list above, you need to restart your app to see that change.

## Roadmap

* Support for Android hardware.
* Support for iOS hardware.
* Support for Android emulators.
* More kinds of code and changes.