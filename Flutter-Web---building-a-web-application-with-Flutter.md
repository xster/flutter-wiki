## Current Status

Flutter now has early experimental support for running flutter web applications. There are still many missing features and known performance issues, so it isn't recommended for production use. This requires the latest flutter master from at least commit [4c0145](https://github.com/flutter/flutter/commit/4c0145d97bd7f48843b360923f59ed30811b7f23).

**IMPORTANT:** The unforked flutter web workflow is still missing several features currently present in flutter_web. Work is ongoing to upstream these changes in Flutter proper.

### Examples

Only the flutter gallery and hello_world have been configured to work with flutter for web. The additional configuration can be generated with the command  `flutter create --web .`.

### Tooling

The web workflow is still gated behind the environment variable FLUTTER_WEB, which must be set to true in the shell in question. (The env check will be removed for the command line shortly https://github.com/flutter/flutter/pull/33867 lands and in IDE's much later). If this is setup correctly, then flutter devices will output a device named web. flutter doctor will also check for a locatable chrome executable which is required for development mode and tests. If one cannot be found, try setting the CHROME_EXECUTABLE environment variable to the path to your Chrome executable.

```
$ flutter devices
> 1 connected device:
> 
> web • web • web-javascript • Google Chrome 75.0.3770.80 
```

```
$ flutter doctor
> Doctor summary (to see all details, run flutter doctor -v):
> [✓] Flutter (Channel unknown, v1.7.3-pre.52, on Mac OS X 10.14.5 18F132, locale en-US)
>  
> [✓] Android toolchain - develop for Android devices (Android SDK version 28.0.3)
> [✓] Xcode - develop for iOS and macOS (Xcode 10.0)
> [✓] iOS tools - develop for iOS devices
> [✓] Chrome - develop for the web
>     ! CHROME_EXECUTABLE not set
> [!] Android Studio (version 3.3)
>     ✗ Flutter plugin not installed; this adds Flutter specific functionality.
>     ✗ Dart plugin not installed; this adds Dart specific functionality.
> [✓] IntelliJ IDEA Community Edition (version 2018.3.4)
> [✓] VS Code (version 1.35.0)
> [✓] Connected device (1 available)
```

flutter run -d web or just flutter run (with no other devices) will launch the application using dartdevc in a Chrome browser.  To see additional build runner output,  you can run with -v. If there are issues with changes not propagating or compilation errors that can't be resolved, run `flutter clean` and then file a bug describing what you changed.

This includes a basic hot restart functionality: though IDEs do support hot reload on save, this functionality does not exist yet for flutter web. While flutter web intends to support modern browsers for shipped applications, for development we require the use of a chrome browser to make use of Chrome devtools.

### Plugins

Plugins are not currently supported in flutter web.

### Engine development

Development on the web engine requires the same setup as the normal engine workflow. Follow the setup in the engine contributing guide at [Setting-up-the-Engine-development-environment](https://github.com/flutter/flutter/wiki/Setting-up-the-Engine-development-environment).

To build the web engine:

```
./flutter/tools/gn --unopt --full-dart-sdk
ninja -C out/host_debug_unopt
```

And then to run with a locally built web engine:

```
flutter run -d web --local-engine=host_debug_unopt
```

The web implementation of `dart:ui` is located at https://github.com/flutter/engine/tree/master/lib/stub_ui and the rules to build the sdk are located at https://github.com/flutter/engine/tree/master/web_sdk.

Subsequent changes to the dart sdk in the engine will require another run of `ninja -C out/host_debug_unopt` and a restart or browser refresh. Currently the web compilers do not recompile the web engine, and instead it works because the dart sdk is loaded as a separate javascript bundle in the browser during development mode.

### Release builds

A release build uses dart2js instead of the development compiler to produce a single JavaScript file. This can be run with the release flag or built using `flutter build web`. This will output files at build/web, including the assets, which need to be served together. There is no support for "building" a debug build, since they consist of potentially thousands of small files.

### Tests

Unit tests can be run on the web with flutter test --platform=chrome. There are still compatibility issues with many flutter tests and bugs with the test bootstrap itself that will need to be resolved, including the engine roll https://github.com/flutter/flutter/pull/34062.

There is no current support for driver tests.
