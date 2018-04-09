## Dart 2

Dart 2 support was opt-in in Flutter beta 1; in beta 2, Dart 2 is enabled by default. We strongly encourage 
you to develop using Dart 2, including updating your applications and packages to incorporate any [breaking changes](https://groups.google.com/forum/#!topic/flutter-dev/H8dDhWg_c8I) you may experience.

## How to update your code

A few tips on how to resolve Dart 2 issues in existing Flutter code:

- Make sure to update both the Flutter SDK to beta 2, and your [Flutter IDE plugin](/get-started/editor/)
  for Android Studio, IntelliJ, or VS Code.

- Validate that your code passes static analysis (from the terminal, run `flutter analyze`,
  or use the [problems view](https://flutter.io/using-ide/) in Android Studio/IntelliJ or VS Code). 

- Consult the comprehensive guidance in the [breaking change notification](https://groups.google.com/forum/#!topic/flutter-dev/H8dDhWg_c8I)
  for how to resolve issues you may see with the new, much more comprehensive
  runtime type checks in Dart 2.
