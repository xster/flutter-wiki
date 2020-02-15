# Contents

Depending on the platform you are making changes for, you may be interested in all or only some of the sections below:

* [Compiling for Android](#compiling-for-android-from-macos-or-linux)
* [Compiling for iOS (from macOS)](#compiling-for-ios-from-macos)
* [Compiling for macOS or Linux](#compiling-for-macos-or-linux)
* [Compiling for Windows](#compiling-for-windows)
* [Compiling for the Web](#compiling-for-the-web)

## Compiling for Android (from macOS or Linux)

These steps build the engine used by `flutter run` for Android devices.

Run the following steps, from the `src` directory created in [Setting up the Engine development environment](https://github.com/flutter/flutter/wiki/Setting-up-the-Engine-development-environment):

1. `git pull upstream master` in `src/flutter` to update the Flutter Engine repo.

2. `gclient sync` to update dependencies.

3. Prepare your build files
    * `./flutter/tools/gn --android --unoptimized` for device-side executables.
    * `./flutter/tools/gn --android --unoptimized --android-cpu=arm64` for newer 64-bit Android devices.
    * `./flutter/tools/gn --android --android-cpu x86 --unoptimized` for x86 emulators.
    * `./flutter/tools/gn --android --android-cpu x64 --unoptimized` for x64 emulators.
    * `./flutter/tools/gn --unoptimized` for host-side executables, needed to compile the code.

4. Build your executables
    * `ninja -C out/android_debug_unopt` for device-side executables.
    * `ninja -C out/android_debug_unopt_arm64` for newer 64-bit Android devices.
    * `ninja -C out/android_debug_unopt_x86` for x86 emulators.
    * `ninja -C out/android_debug_unopt_x64` for x64 emulators.
    * `ninja -C out/host_debug_unopt` for host-side executables.
    * These commands can be combined. Ex: `ninja -C out/android_debug_unopt && ninja -C out/host_debug_unopt`
    * For MacOS, you will need older version of XCode(9.4 or below) to compile android_debug_unopt and android_debug_unopt_x86. If you only care about x64, you can ignore this
    * For Googlers, consider also using the option `-j 100` to parallelize the build using Goma.

This builds a debug-enabled ("unoptimized") binary configured to run Dart in
checked mode ("debug"). There are other versions, see [[Flutter's modes]].

If you're going to be debugging crashes in the engine, make sure you add
`android:debuggable="true"` to the `<application>` element in the
`android/AndroidManifest.xml` file for the Flutter app you are using
to test the engine.

See [[The flutter tool]] for instructions on how to use the `flutter` tool with a local engine.
You will typically use the `android_debug_unopt` build to debug the engine on a device, and
`android_debug_unopt_x64` to debug in on a simulator.

Note that if you use particular android or ios engine build, you will need to have corresponding 
host build available next to it: if you use `android_debug_unopt`, you should have built `host_debug_unopt`,
`android_profile` -> `host_profile`, etc. One caveat concerns cpu-flavored builds like `android_debug_unopt_x86`: you won't be able to build `host_debug_unopt_x86` as that configuration is not supported. What you are expected to do is to build `host_debug_unopt` and symlink `host_debug_unopt_x86` to it.

### Compiling everything that matters on Linux

The following script will update all the builds that matter if you're developing on Linux and testing on Android and created the `.gclient` file in `~/dev/engine`:

```bash
set -ex

cd ~/dev/engine/src/flutter
git fetch upstream
git rebase upstream/master
gclient sync
cd ..

flutter/tools/gn --unoptimized --runtime-mode=debug
flutter/tools/gn --android --unoptimized --runtime-mode=debug
flutter/tools/gn --android --runtime-mode=profile
flutter/tools/gn --android --runtime-mode=release

cd out
find . -mindepth 1 -maxdepth 1 -type d | xargs -n 1 sh -c 'ninja -C $0 || exit 255'
```
For `--runtime-mode=profile` build, please also consider adding `--no-lto` option to the `gn` command. It will make linking much faster with a small sacrifice on the binary size and memory usage (which probably doesn't matter for debugging or performance benchmark purposes.)

## Compiling for iOS (from macOS)

These steps build the engine used by `flutter run` for iOS devices.

Run the following steps, from the `src` directory created in the steps above:

1. `git pull upstream master` in `src/flutter` to update the Flutter Engine repo.

2. `gclient sync` to update dependencies.

3. `./flutter/tools/gn --ios --unoptimized` to prepare build files for device-side executables (or `--ios --simulator --unoptimized` for simulator).
  * This also produces an Xcode project for working with the engine source code at `out/ios_debug_unopt`
  * For a discussion on the various flags and modes, see [[Flutter's modes]].

4. `./flutter/tools/gn --unoptimized` to prepare the build files for host-side executables.

5. `ninja -C out/ios_debug_unopt && ninja -C out/host_debug_unopt` to build all artifacts (use `out/ios_debug_sim_unopt` for Simulator).
    * For Googlers, consider also using the option `-j 100` to parallelize the build using Goma.

See [[The flutter tool]] for instructions on how to use the `flutter` tool with a local engine.
You will typically use the `ios_debug_unopt` build to debug the engine on a device, and
`ios_debug_sim_unopt` to debug in on a simulator.

If you are debugging crashes in the engine, you can connect the `LLDB` debugger from `Xcode` by opening `ios/Runner.xcworkspace` and starting the application by clicking the Run button (CMD + R).

To debug non-crashing code, open Xcode with `ios/Runner.xcworkspace`, expand Flutter->Runner->Supporting Files->main.m in the Runner project. Put a breakpoint in main() then set your desired breakpoint in the engine in [lldb](https://lldb.llvm.org/tutorial.html) via `breakpoint set -...`.

Using Xcode in this way will set a `$LOCAL_ENGINE` variable in your `Generated.xcconfig` file for the Flutter application. This variable will be set until you run `flutter run` again with either a different `--local-engine` option, or with none at all (which will unset it). This is important to note if you plan to re-run your Flutter `Runner.xcodeproj` directly from Xcode.


## Compiling for macOS or Linux

These steps build the desktop embedding, and the engine used by `flutter test` on a host workstation.

1. `git pull upstream master` in `src/flutter` to update the Flutter Engine repo.

2. `gclient sync` to update your dependencies.

3. `./flutter/tools/gn --unoptimized` to prepare your build files.
   * `--unoptimized` disables C++ compiler optimizations. On macOS, binaries are emitted unstripped; on Linux, unstripped binaries are emitted to an `exe.unstripped` subdirectory of the build.

4. `ninja -C out/host_debug_unopt` to build a desktop unoptimized binary.
    * If you skipped `--unoptimized`, use `ninja -C out/host_debug` instead.
    * For Googlers, consider also using the option `-j 1000` to parallelize the build using Goma.

See [[The flutter tool]] for instructions on how to use the `flutter` tool with a local engine.
You will typically use the `host_debug_unopt` build in this setup.


## Compiling for Windows

You can only build selected binaries on Windows (mainly `gen_snapshot` and the desktop embedding).

1. Make sure you have Visual Studio installed (non-Googlers only).

2. `git pull upstream master` in `src/flutter` to update the Flutter Engine repo.

3. `gclient sync` to update your dependencies.

4. `python .\flutter\tools\gn --unoptimized` to prepare your build files.
   * If you are only building `gen_snapshot`: `python .\flutter\tools\gn [--unoptimized] --runtime-mode=[debug|profile|release] [--android]`.

5. `ninja -C .\out\<dir created by previous step>` to build.
   * If you used a non-debug configuration, use `ninja -C .\out\<dir created by previous step> gen_snapshot`.
     Release and profile are not yet supported for the desktop shell.

## Compiling for the Web

For building the engine for the Web we use the [felt](https://github.com/flutter/engine/blob/master/lib/web_ui/dev/README.md) tool.

To test Flutter with a local build of the Web engine, add `--local-engine=host_debug_unopt` to your `flutter` command, e.g.:

```
flutter run --local-engine=host_debug_unopt -d chrome
flutter test --local-engine=host_debug_unopt test/path/to/your_test.dart
```

## Compiling for the Web on Windows

Compiling the web engine might take a few extra steps on Windows. Use cmd.exe and "run as administrator".

1. Make sure you have Visual Studio installed. Set the following environment variables. For Visual Studio use the path of the version you installed.
   * `GYP_MSVS_OVERRIDE_PATH = "C:\Program Files (x86)\Microsoft Visual Studio\2019\Community"`
   * `GYP_MSVS_VERSION = 2017`
2. Make sure, depot_tools, ninja and python are installed and added to the path. Also set the following environment variable for depot tools:
   * `DEPOT_TOOLS_WIN_TOOLCHAIN = 0`
   * Tip: if you get a python error try to use Python 2 instead of 3
3. `git pull upstream master` in `src/flutter` to update the Flutter Engine repo.
4. `gclient sync` to update your dependencies. 
   * Tip: If you get a git authentication errors on this step try Git Bash instead
5. `python .\flutter\tools\gn --unoptimized --full-dart-sdk` to prepare your build files.
6. `ninja -C .\out\<dir created by previous step>` to build.

To test Flutter with a local build of the Web engine, add `--local-engine=host_debug_unopt` to your `flutter` command, e.g.:

```
flutter run --local-engine=host_debug_unopt -d chrome
flutter test --local-engine=host_debug_unopt test/path/to/your_test.dart
```

For testing the engine again use [felt](https://github.com/flutter/engine/blob/master/lib/web_ui/dev/README.md) tool 
this time with felt_windows.bat.

```
felt_windows.bat test
```

_See also: [[Debugging the engine]], which includes instructions on running a Flutter app with a local engine._