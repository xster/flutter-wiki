# iOS & MacOS

Dynamically linked libraries are automatically loaded by the dynamic linker when the App starts. Their constituent symbols can be resolved via `DynamicLibrary.process`.
It is also possible to get a handle for the library via `DynamicLibrary.open` to restrict the scope of symbol resolution, but it's unclear how Apple's review process treats this.

Symbols statically linked into the application binary can be resolved via `DynamicLibrary.executable` or `DynamicLibrary.process`.

## Platform library

To link against a platform library, open `Runner.xcodeproj` in Xcode, select the target, click "+" in the "Linked Frameworks and Libraries" section, and select the platform library to link against.

## First-party library

A first-party native library can be included either as source or as a (signed) `.framework`. It's probably possible to include statically linked archives as well, but I did not attempt this.

### Source

Add the C/C++/Objective-C/Swift sources to the Xcode project and add the following prefix to the exported symbol declarations to ensure they are visible to Dart:

C/C++/Objective-C:
```c
extern "C" /* <= C++ only */ __attribute__((visibility("default"))) __attribute((used))
```

Swift:
```swift
@_cdecl("myFunctionName")
```

### Compiled (dynamic library)

If a (properly signed) `Framework` file is present, add it to the "Embedded Binaries" and "Linked Frameworks & Libraries" section of the target in Xcode.

## Open-source third-party library

Simply add the native code to the `source_files` field of the `.podspec` in the Flutter plugin.
The native code will be statically linked into application binary of any downstream app.

## Closed-source third-party library

Add a `vendored_frameworks` field to the `.podspec` of the Flutter plugin. See the [Cocoa Pods Example](https://github.com/CocoaPods/CocoaPods/blob/master/examples/Vendored%20Framework%20Example/Example%20Pods/VendoredFrameworkExample.podspec).

Note that binary code should not be uploaded to Pub directly but rather downloaded from a trusted third-party as in the example.

# Android

## Platform library

See the [Android documentation](https://developer.android.com/ndk/guides/stable_apis) for a list of stable native APIs.

`DynamicLibrary.open` can be used to load one provided the library name from the above list. For example, to load OpenGL ES (v3):

```dart
DynamicLibrary.open("libGLES_v3.so");
```

You may need to update the Android manifest file of the app or plugin if indicated by the documentation.

## First-party library

The process for including native code in source or binary form is the same for an app or plugin.

## Open-source third-party library

Follow the [Android documentation](https://developer.android.com/studio/projects/add-native-code) for adding native code and support for the native code toolchain (either CMake or `ndk-build`).

## Closed-source third-party library

Add the AAR artifact as a dependency in your `build.gradle`.
It should be downloaded from a repository such as JCenter, not included in your Flutter package.