This page has some hints about debugging the engine.

See also [[Crashes]] for advice on handling engine crashes (specifically around obtaining stack traces, and reporting crashes in AOT Dart code).

## Running a Flutter app with a local engine

Once the appropriate version of the engine is built, run your Flutter app with:

`flutter run --local-engine=XXXX`

to run an app with the local engine where `XXXX` should be replaced with the version you wish to use. For example, use `--local-engine=android_debug_unopt` to run a debug android engine or `--local-engine=ios_debug_sim_unopt` to run a debug iOS simulator engine.

It is important to always have a `host_XXXX` version of the engine built when using a local engine since Flutter uses the host build's version of Dart.

## Bisecting a roll failure

If the engine roll is failing (see [Autorollers]), you can use `git bisect` on the engine repo to track down the offending commit, using the `--local-engine` command as described above to run the failing framework test with each version of the engine.

## Tracing OpenGL calls in Skia

All OpenGL calls in Skia are guarded by either the `GR_GL_CALL_NOERRCHECK` or `GR_GL_CALL_RET_NOERRCHECK` macros. Trace events may be added in these macros to trace all GL calls made by Skia, for example [in a patch like this](https://gist.github.com/chinmaygarde/607eb86d5447615b9cf2804a4f8fb1ce).

Due to the number of events traced to the timeline, the trace buffer may be filled up very quickly. Unless you want to see only the traces for the past few frames, use an endless trace buffer (`flutter --trace-startup` turns on an endless trace buffer).

Also, make sure to run your application with the `--trace-skia` flag.

## Debugging iOS builds with Xcode

If you open your iOS .xcodeproject or .xcworkspace in Xcode, you can set breakpoints e.g. in `main.m`.  From there, you can use `lldb` to set a breakpoint on the relevant engine source, e.g. `(lldb) br set -f FlutterViewController.mm -l 123`.

## Debugging Android builds with gdb

See https://github.com/flutter/engine/blob/master/sky/tools/flutter_gdb#L13

## Debugging Android builds with Android Studio

First, import the Android embedding into Android studio.

1. Import the `engine/src/flutter/shell/platform/android` subdirectory as a new project. It's important to pick this specific directory. IntelliJ needs this as the root in order to make sense of the package structure.
2. Mark the project as depending on the engine's SDK and Java versions (currently 29 and 8). The option should be visible under `File > Project Structure > Project Settings > Project`.
3. (Optional) Manually tell the IDE to look for any JARs needed by the embedding code in `engine/src/third_party` to fix "Missing import" errors. This needs to be done manually and for each needed `third_party` directory. The critical one is `third_party/android_support` for all the support libraries. The option should be visible under `File > Project Structure > Modules`, then by selecting the `android` module and clicking on the `Dependencies` tab.

Next, build and run a flutter app using `flutter run --local-engine`. It may be helpful to [configure the Android app to wait for the local debugger on start](https://developer.android.com/reference/android/os/Debug.html#waitForDebugger()).

Then hit the "Attach debugger" button in Android Studio, click "Show all processes" in the pop up, and select your app from the list and hit OK.

## Logging in the engine

Flutter tool will by default parse out any non-error output from the engine. Error logs will be displayed. Logging is handled though the FML library's `logging.h`