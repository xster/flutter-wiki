*************

`flutter generate` is deprecated and slated to be removed in a future flutter release. `build runner` is now much easier to use directly, and can be used to generate code for flutter projects.


See also: https://pub.dev/packages/build_runner



*************








The flutter tool can automatically generate code during the hot reload workflow, and while building an Application. Learn how to take advantage of this below.

## Generating code on hot reload

To get started, you can add the following snippets into an existing flutter application or through a new flutter create project. We're going to use the `json_widget` builder, which converts json files into widgets. The code for json_widget is available on pub or at https://github.com/jonahwilliams/json_widget

***Step 1***: Add the `json_widget` dependency to the `builders` section of the your pubspec.yaml

pubspec.yaml
```yaml
name: example

...

builders:
  json_widget: any
```


***Step 2***: Create a JSON file called `example.dart` in your project's `lib/` directory. It should contain the following snippet that declares a new widget using JSON instead of dart.

lib/example.json
```json
{
    "name": "Container",
    "params": {
        "color": "#Color(0xFF22DD11)",
        "child": {
            "name": "Text",
            "params": {
                "0": "Hello, World"
            }
        }
    }
}
```

***Step 3***: Import the generated file into main.dart and place it in the widget tree. It is common to import generated libraries with a prefix, but this isn't necessary. Note that the file we imported is called `example.dart` and not `example.json`. This is because code generation does not overwrite old files, it only writes new ones. The `json_widget` builder always produces a dart file with the same name as the corresponding JSON file.

The analyzer or IDE may complain that the file doesn't exist or hasn't been generated yet. We can ignore this warning for now.

lib/main.dart
```dart
import 'package:flutter/material.dart';
import 'example.dart' as generated; // May not exist, it is okay!

void main() => runApp(MyApp());

class MyApp extends StatelessWidget {
  @override
  Widget build(BuildContext context) {
    return MaterialApp(
      title: 'Flutter Demo',
      theme: ThemeData(
        primarySwatch: Colors.blue,
      ),
      home: Scaffold(
        body: ListView(
          children: [
            // use our generated widget
            generated.GeneratedWidget(),
          ]
        ),
      ),
    );
  }
}
```

***Step 4***: Run the application from the IDE or command line

If you followed the steps above correctly, your application should start as normal. You may see `generating build script...` in the console logs - this will only run when the builders section of the `pubspec.yaml` changes.

Any changes made to the JSON file will result in the UI being updated when you hot reload, just like it was a regular Dart file. Try changing "Hello, World" to "Hello, World 2" and hot reloading.

## Writing your own builders

To create your own code generation, you need to implement the Builder interface from package:build/build.dart. This has two required methods: 
  - `buildExtensions` which describes the outputs produced by their file suffix
  - `build` which performs the actions of generating the code.

This file should be in a separate package and should also expose a top level method that returns an instance of your builder - more on this below.

```dart
import 'package:build/build.dart';

/// The builder factory used by the `build.yaml` script.
Builder simpleBuilder(BuilderOptions options) => SimpleBuilder();

/// A trivial builder which copies the contents of a `spec` file into a `dart` file.
class SimpleBuilder extends Builder {
  @override
  Map<String, List<String>> get buildExtensions => const <String, List<String>>{'.spec' : <String>['.dart']};

  @override
  Future<void> build(BuildStep buildStep) async {
    // The asset id identifies
    final AssetId output = buildStep.inputId.changeExtension('.dart');
    final String contents = await buildStep.readAsString(buildStep.inputId);
    buildStep.writeAsString(output, contents);
  }
}
```

The builder needs to be combined with a build.yaml that informs the build_runner how to use your builder. This should be placed in your package's root directory. For the builder above we need the following configuration. Note that the simpleBuilder name under the builder factories key matches the name of the top level method we declared.

build.yaml
```yaml
# Read about `build.yaml` at https://pub.dev/packages/build_config
builders:
  simple:
    import: "package:simple_codegen/builders.dart"
    builder_factories:
      - simpleBuilder
    build_extensions: {'.spec':['.dart']}
    auto_apply: all_packages
```