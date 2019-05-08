## Linting for Flutter apps for Chrome OS

_Note:_ this feature is not yet available in the Flutter stable channel.

### What is it?

Flutter tooling will soon include a new set of lint rules to catch violations of
Chrome OS best practice guidelines for Android and Flutter apps.

### What does it do?

In your IDE (or when running `flutter analyze`), you'll see lints if your Flutter
app would have issues targeting Chrome OS.

```
Analyzing ...                                                      
warning • This hardware feature is not supported on Chrome OS • android/app/src/main/AndroidManifest.xml:4:33 • unsupported_chrome_os_hardware
```

### For what version of Flutter?

Currently, you'll need to be on the tip-of-tree of Flutter to try this out. This will likely graduate shortly to the `dev` channel, and later, to `beta` and `stable`.

`flutter channel master && flutter upgrade`

### How do I enable this?

To turn this on for your Flutter app, create an file named `analysis_options.yaml`
in the root of your Flutter project. The contents should look similar to this:

```yaml
include: package:flutter/analysis_options_user.yaml

analyzer:
 optional-checks:
   chrome-os-manifest-checks
```
	
The warnings will show in the Dart Analysis view in IntelliJ and Android Studio or the
Problems view in VS Code.
