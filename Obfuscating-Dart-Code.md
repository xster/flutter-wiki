Code obfuscation hides function and class names in your compiled Dart code, making it difficult for an attacker to reverse engineer your proprietary app.

Note that Dart obfuscation has not yet been thoroughly tested. Please [file a bug](https://github.com/flutter/flutter/issues) if you experience any issues. For more information, see [this issue on Stack Overflow](https://stackoverflow.com/questions/50542764/how-to-obfuscate-flutter-apps).

## iOS

Add the following line to `<ProjectRoot>/android/gradle.properties`:

```
extra-gen-snapshot-options=--obfuscate
```

## Android

### Step 1 - Modify the "build aot" call

Add the following flag to the `build aot` call in the
`<ProjectRoot>/packages/flutter_tools/bin/xcode_backend.sh` file:

```
${extra_gen_snapshot_options_or_none}
```

Define this flag as follows:

```
local extra_gen_snapshot_options_or_none=""
if [[ -n "$EXTRA_GEN_SNAPSHOT_OPTIONS" ]]; then
  extra_gen_snapshot_options_or_none="--extra-gen-snapshot-options=$EXTRA_GEN_SNAPSHOT_OPTIONS"
fi
```

### Step 2 - Apply your changes

In the project root of your app, run the following two commands:

```
run git commit -am "Enable obfuscation on iOS"
flutter
```

### Step 3 - Modify the release config

In `<ProjectRoot>/ios/Flutter/Release.xcconfig>`, add the
following line:

```
EXTRA_GEN_SNAPSHOT_OPTIONS=--obfuscate
```

For information on obfuscating the Android wrapper, see [Minify and obfuscate](https://flutter.io/android-release/#minify-and-obfuscate) in [Preparing an Android App for Release](https://flutter.io/android-release/#minify-and-obfuscate).