This guide is for updating projects that currently use `package:flutter_web` to use the Flutter SDK.

## Ensure you have Flutter >= 1.9

At the time of writing (September 13th, 2019), you'll need to switch to the `dev` channel to use web support in the Flutter SDK.

```bash
$ flutter channel dev
$ flutter upgrade
```

You can verify your flutter version using:

```bash
$ flutter version
Flutter 1.10.1 • channel dev • https://github.com/flutter/flutter.git
Framework • revision ce45c2d3e6 (7 days ago) • 2019-09-06 20:11:41 -0400
Engine • revision b9ce2500d9
Tools • Dart 2.5.0 (build 2.5.0-dev.4.0 be66176534)
```

## Enable web support

To enable web support you'll need to run the following command:

```bash
flutter config --enable-web
```

## Clean up the web/ directory

You might have a `web/main.dart` file that looks like:

```dart
import 'package:flutter_web_ui/ui.dart' as ui;
import 'package:example/main.dart' as app;

main() async {
 await ui.webOnlyInitializePlatform();
 app.main();
}
```

Remove these files. An equivalent `web/` directory will be created automatically in the next step.

## Initialize the web/ directory

Flutter has a web runner that can be initialized using the `flutter create` command:

```bash
flutter create .
```

This should create a new `web/index.html` for your project.

## Update the pubspec.yaml

Change:

```yaml
dependencies:
  flutter_web: any
  flutter_web_ui: any

```

to:

```yaml
dependencies:
  flutter:
    sdk: flutter
```

## Update the font configuration

Remove the `FontManifest.json` file and specify the fonts the standard way. See this [cookbook article](https://flutter.dev/docs/cookbook/design/fonts) for an example.

## Move the assets

Assets should be placed in a top-level `assets/` directory and specified in the `pubspec.yaml`:

```yaml
flutter:
 assets:
   - preview.png
```

Alternatively, an entire top-level directory can be used, for example, `my_assets`. In this configuration, the directory must be specified in the pubspec:

```yaml
flutter:
 assets:
   - my_assets/
```

## Change imports

Any Dart files using `flutter_web` should use `package:flutter` instead. For example:

```dart
import 'package:flutter_web/material.dart';
import 'package:flutter_web_ui/ui.dart';
```

Changes to:

```dart
import package:'flutter/material.dart';
import 'dart:ui';
```

## Use Flutter SDK to run apps

Instead of using `webdev serve `or  `pub run build_runner serve`, use `flutter run -d chrome`


