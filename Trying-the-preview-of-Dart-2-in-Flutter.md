# Deprecation Notice
**Note**: As of Flutter beta 2 and later, Dart 2 is on by default. This article only applies to older versions of Flutter, and will be deleted as the last few users upgrade to more current releases.

## Previewing Dart 2 with Flutter beta 1
The following steps apply to Flutter beta 1:

To preview Dart 2 you need a recent build of the Flutter SDK from one of the following [channels](https://github.com/flutter/flutter/wiki/Flutter-build-release-channels):

* Beta channel: build 0.1.4 from 2018-02-19, or later
* Dev channel: build from 2018-02-22, or later
* Master channel: build from 2018-02-20, or later

To view your current version, run `flutter --version`. Sample version output with the key values highlighted:

`$ flutter --version`<br>
`Flutter `**`0.1.4 • channel beta`**`• git@github.com:flutter/flutter.git`<br>
`Framework • revision f914e701c5 (7 days ago) •` **`2018-02-19 21:12:17 -0800`**<br>
`Engine • revision 13cf22c284`<br>
`Tools • Dart 2.0.0-dev.27.0-flutter-0d5cf900b0`

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
1. Set the 'Run applications in Dart 2.0:' option to 'Enable Dart 2'
1. Restart Android Studio / IntelliJ

### Visual Studio Code

1. Install/upgrade the Dart Code plugin so that you have version 2.9 or later
1. Open `Code`>`Preferences`>`Settings` (on Windows `File`>`Preferences`>`Settings`)
1. Search for the setting `dart.previewDart2` and set it to true as a user setting (for all projects), or a workspace setting (for current workspace)
1. Click 'Reload Project' in the banner stating that the Dart/Flutter settings have changed (alternatively, restart VS Code)