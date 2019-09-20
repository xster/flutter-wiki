# Walkthrough: binding to bundled C/C++ sources in a plugin

This tutorial demonstrates how to bundle C/C++ sources in a Flutter plugin and
bind to them via the Dart FFI on both Android and iOS.

We will create a C function implementing 32-bit addition and expose it through a
Dart plugin named "native_add".

## Step 1: Create a plugin

Skip this step if you already have a plugin.

```bash
flutter create --template=plugin native_add
cd native_add
```

## Step 2: Add C/C++ sources

We need to inform both the Android and iOS build systems about the native code
so they can be compiled and linked appropriately into the final application.

We add the sources to the `ios` folder, because CocoaPods doesn't allow
including sources above the podspec, whereas Gradle will allow us to point it to
the `ios` folder. It's not required to use the same sources for both iOS and
Android; you may of course add Android-specific sources in the `android` folder
and modify the `CMakeLists.txt` appropriately.

The FFI can only bind against C symbols, so in C++ these symbols must be marked
"`extern C`". We should also add attributes to indicate that the symbols are
referenced from Dart, to prevent a linker from discarding the symbols during
link-time optimization.

For example:
```bash
cat >ios/Classes/native_add.cpp <<EOF
#include <stdint.h>

extern "C" __attribute__((visibility("default"))) __attribute((used))
int32_t native_add(int32_t x, int32_t y) {
    return x + y;
}
EOF
```

The podspec (`ios/native_add.podspec`) will automatically include all C/C++
files in `ios/Classes`, and they will be compiled within the Xcode build system.

On Android, however, we need to create a `CMakeLists.txt` to inform define how
the sources should be compiled and point Gradle to it.

```bash
cat >android/CMakeLists.txt <<EOF
cmake_minimum_required(VERSION 3.4.1)  # For example.

add_library( native_add

             # Sets the library as a shared library.
             SHARED

             # Provides a relative path to your source file(s).
             ../ios/Classes/native_add.cpp )
EOF
```

Finally, add an `externalNativeBuild` section to `android/build.grade`, for
example:
```groovy
android {
  // ...
  externalNativeBuild {
    // Encapsulates your CMake build configurations.
    cmake {
      // Provides a relative path to your CMake build script.
      path "CMakeLists.txt"
    }
  }
  // ...
}
```

## Step 3: Load the code via the FFI

In our example, we can add the following code to `lib/native_add.dart`.
However the location of the Dart binding code is not important.

First, we must create a `DynamicLibrary` handle to the native code. This step
varies between iOS vs Android:

```dart
import 'dart:ffi';  // For FFI
import 'dart:io';  // For Platform.isX

final DynamicLibrary nativeAddLib =
  Platform.isAndroid
    ? DynamicLibrary.open("libnative_add.so")
    : DynamicLibrary.open("native_add.framework/native_add");
```

Note that on Android the native library is named by the `CMakeLists.txt` (see
above), whereas on iOS it takes the plugin's name.

With a handle to the enclosing library, we can resolve the `native_add` symbol:

```dart
final int Function(int x, int y) nativeAdd =
  nativeAddLib
    .lookup<NativeFunction<Int32 Function(Int32, Int32)>>("native_add")
    .asFunction();
```

Finally we can call it. To demonstrate this within the auto-generated "example"
app (`example/lib/main.dart`):

```dart
// Inside of _MyAppState.build:
        body: Center(
          child: Text('1 + 2 == ${nativeAdd(1, 2)}'),
        ),
```

# Other use cases

## iOS & MacOS

Dynamically linked libraries are automatically loaded by the dynamic linker when the App starts. Their constituent symbols can be resolved via `DynamicLibrary.process`.
It is also possible to get a handle for the library via `DynamicLibrary.open` to restrict the scope of symbol resolution, but it's unclear how Apple's review process treats this.

Symbols statically linked into the application binary can be resolved via `DynamicLibrary.executable` or `DynamicLibrary.process`.

### Platform library

To link against a platform library, open `Runner.xcodeproj` in Xcode, select the target, click "+" in the "Linked Frameworks and Libraries" section, and select the platform library to link against.

### First-party library

A first-party native library can be included either as source or as a (signed) `.framework`. It's probably possible to include statically linked archives as well, but I did not attempt this.

#### Source

Add the C/C++/Objective-C/Swift sources to the Xcode project and add the following prefix to the exported symbol declarations to ensure they are visible to Dart:

C/C++/Objective-C:
```c
extern "C" /* <= C++ only */ __attribute__((visibility("default"))) __attribute((used))
```

Swift:
```swift
@_cdecl("myFunctionName")
```

#### Compiled (dynamic library)

If a (properly signed) `Framework` file is present, add it to the "Embedded Binaries" and "Linked Frameworks & Libraries" section of the target in Xcode.

### Open-source third-party library

Simply add the native code to the `source_files` field of the `.podspec` in the Flutter plugin.
The native code will be statically linked into application binary of any downstream app.

### Closed-source third-party library

Add a `vendored_frameworks` field to the `.podspec` of the Flutter plugin. See the [Cocoa Pods Example](https://github.com/CocoaPods/CocoaPods/blob/master/examples/Vendored%20Framework%20Example/Example%20Pods/VendoredFrameworkExample.podspec).

Note that binary code should not be uploaded to Pub directly but rather downloaded from a trusted third-party as in the example.

## Android

### Platform library

See the [Android documentation](https://developer.android.com/ndk/guides/stable_apis) for a list of stable native APIs.

`DynamicLibrary.open` can be used to load one provided the library name from the above list. For example, to load OpenGL ES (v3):

```dart
DynamicLibrary.open("libGLES_v3.so");
```

You may need to update the Android manifest file of the app or plugin if indicated by the documentation.

### First-party library

The process for including native code in source or binary form is the same for an app or plugin.

### Open-source third-party library

Follow the [Android documentation](https://developer.android.com/studio/projects/add-native-code) for adding native code and support for the native code toolchain (either CMake or `ndk-build`).

### Closed-source third-party library

Add the AAR artifact as a dependency in your `build.gradle`.
It should be downloaded from a repository such as JCenter, not included in your Flutter package.