This page has some hints about debugging the engine.

See also [[Crashes]] for advice on handling engine crashes (specifically around obtaining stack traces, and reporting crashes in AOT Dart code).

## Tracing OpenGL calls in Skia

All OpenGL calls in Skia are guarded by either the `GR_GL_CALL_NOERRCHECK` or `GR_GL_CALL_RET_NOERRCHECK` macros. Trace events may be added in these macros to trace all GL calls made by Skia, for example [in a patch like this](https://gist.github.com/chinmaygarde/607eb86d5447615b9cf2804a4f8fb1ce).

Due to the number of events traced to the timeline, the trace buffer may be filled up very quickly. Unless you want to see only the traces for the past few frames, use an endless trace buffer (`flutter --trace-startup` turns on an endless trace buffer).

Also, make sure to run your application with the `--trace-skia` flag.

## Debugging iOS builds with Xcode

If you open your iOS .xcodeproject or .xcworkspace in Xcode, you can set breakpoints e.g. in `main.m`.  From there, you can use `lldb` to set a breakpoint on the relevant engine source, e.g. `(lldb) br set -f FlutterViewController.mm -l 123`.