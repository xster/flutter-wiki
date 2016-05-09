We aspire to reach a state where developers are able to use the following modes for running Flutter code. Our tools should not expose any other combinations of features or modes. Each mode corresponds to a separate build of the engine that we provide.

1. Develop mode on device (including simulators, emulators): Turns on all the assertions in the world, includes all debugging information, enables all the debugger aids (e.g. observatory) and service extensions. Optimizes for fast develop/run cycles. Does not optimize for execution speed, binary size, or deployment. Used by `flutter run`.

2. Release mode on device (excluding simulators, emulators): Turns off all assertions, strips as much debugging information as possible, turns off all the debugger tools. Optimizes for fast startup, fast execution, small package sizes. Disables any debugging aids.  Disables service extensions. Intended for deployment to end-users. Used by `flutter run --release`.

3. Profile mode on device (excluding simulators, emulators): Same as release mode except that profile-mode service extensions (like the one that turns on the performance overlay) is enabled, and tracing is enabled, as well as the minimum required to support using the tracing information (e.g. observatory can probably connect to the process). Used by `flutter run --profile`.

4. Headless test mode on desktop: Same as debug mode except headless and for desktop platforms. Used by `flutter test`.

In addition, for our purposes during development, each of the above should be able to be built in two modes: engine-release, which is what end-developers use, and engine-debug, which is what we would use when debugging the engine.