## Dart 2

Dart 2 support was opt-in in Flutter beta 1; in beta 2, Dart 2 is enabled by default, and we strongly encourage 
you to develop using Dart 2, including updating your applications and packages to incorporate any [breaking changes](https://groups.google.com/forum/#!topic/flutter-dev/H8dDhWg_c8I) you may experience.

## How to update your code

- todo:
- [www.dartlang.org/dart-2](https://www.dartlang.org/dart-2)

## Opting out

Should you need to temporarily opt-put of Dart 2, you can use these steps:

### From the Terminal

To opt-out on the command-line:

```
flutter analyze --no-preview-dart-2
```

and:

```
flutter run --no-preview-dart-2
```

### From Android Studio / IntelliJ IDEA

Install or upgrade the Flutter plugin so that you have version 23 or later, then:
1. Create a Flutter project
1. Open **Settings**, and select 'Flutter' under 'Languages & Frameworks'
1. Set the 'Run applications in Dart 2.0:' option to 'Disable Dart 2'
1. Restart Android Studio / IntelliJ

### From Visual Studio Code

1. Install/upgrade the Dart Code plugin so that you have version 2.11 or later
1. Open `Code`>`Preferences`>`Settings` (on Windows `File`>`Preferences`>`Settings`)
1. Search for the setting `dart.previewDart2` and set it to false as a user setting (for all projects), or a workspace setting (for current workspace)
1. Click 'Reload Project' in the banner stating that the Dart/Flutter settings have changed (alternatively, restart VS Code)

**Note**: These opt-outs are provided only temporarily, and are expected to be removed in Flutter beta 3!
