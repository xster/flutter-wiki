## Dart 2 introduction

Dart 2 is an update to the Dart programming language that introduces:
  * [Strong compile-time type checks](https://www.dartlang.org/dart-2#strong-mode-and-static-typing)
  * Optional new/const keyword when calling a constructor (e.g., change `child: new Text('Hi')` to `child: Text('Hi')`
  * Various smaller language changes

Changes that may break Flutter apps and packages have, and will continue to, be communicated to the
[flutter-dev](https://groups.google.com/forum/#!forum/flutter-dev) Google Group as
[breaking changes](https://groups.google.com/forum/#!searchin/flutterdev/subject$3A%22breaking$20change%22%7Csort:date).

## Previewing Dart 2 with Flutter

To preview Dart 2 you need a recent build of the Flutter SDK
(to view your current version, run `flutter --version`):

  * Master channel: build from 2018-02-20 or later
  * Alpha channel: not compatible

### Terminal

To analyze your app with Dart 2 analysis / compile-time checks,
pass the preview flag to the flutter analyze:

```
flutter analyze --preview-dart-2
```

To run your app, and verify if it passes Dart 2 run-time checks,
pass the preview flag to the flutter run command:

```
flutter run --preview-dart-2
```


### Android Studio / IntelliJ IDEA

A version of the Flutter plugin with Dart 2 preview support will be launched soon!

### VS Code

A version of the Dart Code plugin with Dart 2 preview support will be launched soon!