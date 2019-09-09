This guide is for updating projects that currently use `package:flutter_web` to use the Flutter SDK.

## Clean up the web/ directory

You might have a web/main.dart file that looks like:

```dart
import 'package:flutter_web_ui/ui.dart' as ui;
import 'package:example/main.dart' as app;

main() async {
 await ui.webOnlyInitializePlatform();
 app.main();
}
```

Remove these files. An equivalent web/ directory will be created automatically in the next step.

## Initialize the web/ directory

Flutter has a web runner that can be initialized using the `flutter create` command:

```bash
flutter create .
```

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

Remove the FontManifest.json file and specify the fonts the standard way. See this [cookbook article](https://flutter.dev/docs/cookbook/design/fonts) for an example.

## Move the assets

Assets should be placed in a top-level `assets/` directory and specified in the pubspec.yaml:

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


