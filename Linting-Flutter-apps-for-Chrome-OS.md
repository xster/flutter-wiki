## Linting for Flutter apps for Chrome OS

### What is it?

Flutter tooling will soon include a new set of lint rules to catch violations of
Chrome OS best practice guidelines for Android and Flutter apps.

### What does it do?

In your IDE or when running `flutter analyze` at the command line, you'll see lints if your Flutter
app would have issues targeting Chrome OS.

[[images/cros-lint-rules-sample-results.png]]

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
