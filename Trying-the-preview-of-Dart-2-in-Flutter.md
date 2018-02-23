## Dart 2 introduction

Dart 2 is an update to the Dart programming language that introduces:
  * [Strong compile-time type checks](https://www.dartlang.org/dart-2#strong-mode-and-static-typing)
  * Optional new/const keyword when calling a constructor (e.g., change `child: new Text('Hi')` to `child: Text('Hi')`)
  * Various smaller language changes

Changes that may break Flutter apps and packages have, and will continue to, be communicated to the
[flutter-dev](https://groups.google.com/forum/#!forum/flutter-dev) Google Group as
[breaking changes](https://groups.google.com/forum/#!searchin/flutterdev/subject$3A%22breaking$20change%22%7Csort:date).

Dart 2 is large change, and while the latest development builds of Flutter include some of Dart 2, most features are kept behind an opt-in flag while we perform final testing. However, we'd encourage you to turn the flag on during development and file issues if you see unexpected behavioural changes with the flag enabled.

## Previewing Dart 2 with Flutter

To preview Dart 2 you need a recent build of the Flutter SDK:

* Dev [channel](https://github.com/flutter/flutter/wiki/Flutter-build-release-channels): build from 2018-02-22 or later
* Master channel: build from 2018-02-20 or later

To view your current version, run `flutter --version`. Sample version output with the key values highlighted:

`$ flutter --version`<br>
`Flutter 0.1.5-pre.9 •` **`channel master`** `• git@github.com:flutter/flutter.git`<br>
`Framework • revision 21c514fc56 (34 hours ago) •` **`2018-02-20 23:22:58 -0800`**<br>
`Engine • revision ead227f118`<br>
`Tools • Dart 2.0.0-dev.28.0.flutter-0b4f01f759`

### Terminal

To analyze your app with Dart 2 analysis / compile-time checks,
pass the preview flag to flutter analyze:

```
flutter analyze --preview-dart-2
```

To run your app and verify if it passes Dart 2 run-time checks,
pass the preview flag to the flutter run command:

```
flutter run --preview-dart-2
```

### Android Studio / IntelliJ IDEA

Install or upgrade the Flutter plugin so that you have version 22 or later, then:
1. Create a Flutter project
1. Open **Settings**, and select 'Flutter' under 'Languages & Frameworks'
1. Check the 'Run applications with --preview-dart-2' checkbox under 'Experiments'
1. Restart Android Studio / IntelliJ

### Visual Studio Code

1. Install/upgrade the Dart Code plugin so that you have version 2.9 or later
1. Open Code>Settings>Preferences
1. Search for the setting 'dart.previewDart2' and set it to true as a user setting (for all projects), or a workspace setting (for current workspace)
1. Restart VS Code
