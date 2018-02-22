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

* [Master channel](https://github.com/flutter/flutter/wiki/Flutter-build-release-channels): build from 2018-02-20 or later
* Alpha channel: not compatible

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

A version of the Flutter plugin with Dart 2 preview support will be launched shortly! This will include an easy way to opt-into trying out Dart 2.

In the meantime, to try Dart 2 out today:
- For Dart 2 at runtime, open the launch configuration for your application, and add ‘--preview-dart-2’ to the additional arguments field.
- For Dart 2 at analysis time, you’ll need to add an additional flag to the analysis server by editing the IntelliJ registry. Type shift-cmd-A (or shift-ctrl-A) to open the find actions dialog; type ‘registry’ and select the associated entry to open the IntelliJ registry. Then, search for the ‘dart.server.additional.arguments registry key, and add --preview-dart-2 to its value. Restart IntelliJ (or restart the analysis server from the Dart Analysis view) for this setting to take effect.

### Visual Studio Code

A release version of the Dart Code plugin with Dart 2 preview support will be launched soon. In the meantime, you can download the [latest beta version](https://github.com/Dart-Code/Dart-Code/releases/download/v2.9.0-beta.4/dart-code-2.9.0-beta.4.vsix) and install it from Visual Studio by pressing Ctrl-Shift-P or Command-Shift-P to launch the Command Bar and then running the **Extensions: Install from VSIX...** command. 

![Screenshot of the user interface to install extensions](https://i.imgur.com/SxqiFBQ.png)

When the release version of the plug-in is available in the marketplace, you will automatically be upgraded.