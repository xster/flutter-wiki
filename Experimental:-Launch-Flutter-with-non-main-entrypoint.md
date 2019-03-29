_**Everything in this doc and linked from this doc is experimental. These details WILL change. Do not use these instructions or APIs in production code because we will break you.**_

# Launch Flutter with non-main entrypoint

Typically, a Flutter app begins execution at the Dart method called `main()`, however this is not required. Developers can specify a different Dart entrypoint:

```java
// Launches FlutterActivity and runs a Dart method called mySpecialScreen().
Intent defaultFlutter = new FlutterActivity.IntentBuilder()
  .dartEntrypoint("mySpecialScreen")
  .build(currentActivity);
startActivity(defaultFlutter);

// Create a FlutterFragment that runs a Dart method called mySpecialScreen().
FlutterFragment flutterFragment = new FlutterFragment.Builder()
  .dartEntrypoint("mySpecialScreen")
  .build();
```

## Avoid Tree Shaking in Release

When you build in release mode, your Dart code is tree-shaken. This means that the compiler removes any Dart code that it thinks you're not using. This includes your special entrypoints. To avoid crashing in release mode as a result of tree-shaking, be sure to place the following `@pragma` above each of your custom entrypoints.

```dart
@pragma('vm:entry-point')
void mySpecialScreen() {
  // implementation
}
```