_If you've already built the engine and have the configuration set up but merely need a refresher on
actually compiling the code, jump to the relevant sections below._

Make sure you have the following dependencies available:

 * Linux, macOS, or Windows.

     * Linux supports cross-compiling artifacts for Android, but not iOS.

     * macOS supports cross-compiling artifacts for Android and iOS.

     * Windows doesn't support cross-compiling artifacts for either Android or iOS.

 * git (used for source version control).

 * An IDE. [Android Studio with the Flutter plugin](https://flutter.io/using-ide/) is
   our flagship IDE. You can use whatever IDE you feel most comfortable with.

 * An ssh client (used to authenticate with GitHub).

 * Chromium's
   [depot_tools](http://commondatastorage.googleapis.com/chrome-infradocs/flat/depot_tools/docs/html/depot_tools_tutorial.html#_setting_up)
   (make sure it's in your path). We use the `gclient` tool from depot_tools.

 * Python (used by many of our tools, including 'gclient').

 * On macOS and Linux: curl and unzip (used by `gclient sync`).

 * On Windows: Visual Studio (required for non-Googlers only).

 * On macOS: the latest Xcode.

 * Recommended for Googlers: Goma for distributed builds.

You do not need [Dart](https://www.dartlang.org/downloads/linux.html) installed,
as a Dart tool chain is automatically downloaded as part of the "getting the code"
step. Similarly for the Android SDK, it's downloaded by the `gclient sync` step below.

Run the following steps to set up your environment:

1. Fork `https://github.com/flutter/engine` into your own GitHub account. If
   you already have a fork, and are now installing a development environment on
   a new machine, make sure you've updated your fork so that you don't use stale
   configuration options from long ago. Do not clone this repo locally, scripts
   will take care of that for you.

2. If you haven't configured your machine with an SSH key that's known to github then
   follow the directions here: https://help.github.com/articles/generating-ssh-keys/.

3. Create an empty directory for your copy of the repository. For best
   results, call it `engine`: some of the tools assume this name when
   working across repositories. (They can be configured to use other
   names too; this isn't a strict requirement. It just makes things easier.)

4. Create a `.gclient` file in the `engine` directory with the
   following contents, replacing `<your_name_here>` with your GitHub
   account name:

   ```python
   solutions = [
     {
       "managed": False,
       "name": "src/flutter",
       "url": "git@github.com:<your_name_here>/engine.git",
       "custom_deps": {},
       "deps_file": "DEPS",
       "safesync_url": "",
     },
   ]
   ```

5. `cd engine` (Change to the directory in which you put the
   `.gclient` file.)

6. `gclient sync` This will fetch all the source code that Flutter
   depends on. Avoid interrupting this script, it can leave your
   repository in an inconsistent state that is tedious to clean up.
   (This step automatically runs `git clone`, among other things.)

7. `cd src/flutter` (Change to the `flutter` directory of the `src`
   directory that `gclient sync` created in your `engine` directory.)

8. `git remote add upstream git@github.com:flutter/engine.git` (So
   that you fetch from the master `flutter/engine` repository, not
   your clone, when running `git fetch` et al.)

9. `cd ..` (Return to the `src` directory that `gclient sync` created
    in your `engine` directory.)

10. If you're on Linux, run: `sudo ./build/install-build-deps-android.sh`

11. If you're on Linux, run: `sudo ./build/install-build-deps.sh`

12. If you're on Mac, install Oracle's Java JDK, version 1.7 or later.

13. If you're on Mac, install `ant`: `brew install ant`

14. If you're on Windows, install Visual Studio (non-Google developers only).

15. If you're planning on working on the
    [buildroot](https://github.com/flutter/buildroot) repository as
    well, and have a local checkout of that repository, run the
    following commands in the `src` directory to update your git
    remotes accordingly:

    ```bash
    git remote rename origin upstream
    git remote add origin git@github.com:<your_name_here>/buildroot.git
    ```

## Compiling for Android (from macOS or Linux)

These steps build the engine used by `flutter run` for Android devices.

Run the following steps, from the `src` directory created in the steps above:

1. `git pull upstream master` in `src/flutter` to update the Flutter Engine repo.

2. `gclient sync` to update dependencies.

3. Prepare your build files
    * `./flutter/tools/gn --android --unoptimized` for device-side executables.
    * `./flutter/tools/gn --android --android-cpu x86 --unoptimized` for x86 emulators.
    * `./flutter/tools/gn --android --android-cpu x64 --unoptimized` for x64 emulators.
    * `./flutter/tools/gn --unoptimized` for host-side executables, needed to compile the code.

4. Build your executables
    * `ninja -C out/android_debug_unopt` for device-side executables.
    * `ninja -C out/android_debug_unopt_x86` for x86 emulators.
    * `ninja -C out/android_debug_unopt_x64` for x64 emulators.
    * `ninja -C out/host_debug_unopt` for host-side executables.
    * These commands can be combined. Ex: `ninja -C out/android_debug_unopt && ninja -C out/host_debug_unopt`
    * For Googlers, consider also using the option `-j 1000` to parallelize the build using Goma.

This builds a debug-enabled ("unoptimized") binary configured to run Dart in
checked mode ("debug"). There are other versions, see [[Flutter's modes]].

If you're going to be debugging crashes in the engine, make sure you add
`android:debuggable="true"` to the `<application>` element in the
`android/AndroidManifest.xml` file for the Flutter app you are using
to test the engine.

See [[The flutter tool]] for instructions on how to use the `flutter` tool with a local engine.
You will typically use the `android_debug_unopt` build to debug the engine on a device, and
`android_debug_unopt_x64` to debug in on a simulator.

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
flutter/tools/gn --android --unoptimized --runtime-mode=profile
flutter/tools/gn --android --unoptimized --runtime-mode=release
flutter/tools/gn --android --runtime-mode=debug
flutter/tools/gn --android --runtime-mode=profile
flutter/tools/gn --android --runtime-mode=release

cd out
find . -mindepth 1 -maxdepth 1 -type d | xargs -n 1 sh -c 'ninja -C $0 || exit 255'
```


## Compiling for iOS (from macOS)

These steps build the engine used by `flutter run` for iOS devices.

Run the following steps, from the `src` directory created in the steps above:

1. `git pull upstream master` in `src/flutter` to update the Flutter Engine repo.

2. `gclient sync` to update dependencies.

3. `./flutter/tools/gn --ios --unoptimized` to prepare build files for device-side executables (or `--ios --simulator --unoptimized` for simulator).
  * This also produces an Xcode project for working with the engine source code at `out/ios_debug_unopt`
  * For a discussion on the various flags and modes, see [[Flutter's mode]].

4. `./flutter/tools/gn --unoptimized` to prepare the build files for host-side executables.

5. `ninja -C out/ios_debug_unopt && ninja -C out/host_debug_unopt` to build all artifacts (use `out/ios_debug_sim_unopt` for Simulator).
    * For Googlers, consider also using the option `-j 1000` to parallelize the build using Goma.

See [[The flutter tool]] for instructions on how to use the `flutter` tool with a local engine.
You will typically use the `ios_debug_unopt` build to debug the engine on a device, and
`ios_debug_sim_unopt` to debug in on a simulator.

If you are debugging crashes in the engine, you can connect the `LLDB` debugger from `Xcode` by opening `ios/Runner.xcworkspace` and starting the application by clicking the Run button (CMD + R).

To debug non-crashing code, open Xcode with `ios/Runner.xcworkspace`, expand Flutter->Runner->Supporting Files->main.m in the Runner project. Put a breakpoint in main() then set your desired breakpoint in the engine in [lldb](https://lldb.llvm.org/tutorial.html) via `breakpoint set -...`.

Using Xcode in this way will set a `$LOCAL_ENGINE` variable in your `Generated.xcconfig` file for the Flutter application. This variable will be set until you run `flutter run` again with either a different `--local-engine` option, or with none at all (which will unset it). This is important to note if you plan to re-run your Flutter `Runner.xcodeproj` directly from Xcode.


## Compiling a host binary on macOS or Linux

These steps build the engine used by `flutter test` on a host workstation.

1. `git pull upstream master` in `src/flutter` to update the Flutter Engine repo.

2. `gclient sync` to update your dependencies.

3. `./flutter/tools/gn --unoptimized` to prepare your build files.
   * `--unoptimized` disables C++ compiler optimizations and does not strip debug symbols.

4. `ninja -C out/host_debug_unopt` to build a desktop unoptimized binary.
    * If you skipped `--upoptimized`, use `ninja -C out/host_debug` instead.
    * For Googlers, consider also using the option `-j 1000` to parallelize the build using Goma.

See [[The flutter tool]] for instructions on how to use the `flutter` tool with a local engine.
You will typically use the `host_debug_unopt` build in this setup.


## Compiling a host binary on Windows

You can only build selected binaries on Windows (mainly `gen_snapshot`).

1. Make sure you have Visual Studio installed (non-Googlers only).

2. `git pull upstream master` in `src/flutter` to update the Flutter Engine repo.

3. `gclient sync` to update your dependencies.

4. `python .\flutter\tools\gn [--unoptimized] --runtime-mode=[debug|profile|release] [--android]` to prepare your build files.

5. `ninja -C .\out\<dir created by previous step> gen_snapshot` to build.

