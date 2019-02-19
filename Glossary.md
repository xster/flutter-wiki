Here are some terms that we use in the Flutter project and what they mean:

- **Cold restart**. Quitting and restarting an application and its associated runtime. This contrasts with hot restart, where the process and/or runtime would not be terminated.

- **Dynamic extension loading**. The ability to download some Dart code that wasn't written when the app was first published, which adds a new feature to the app. This could be done on the fly. It may require the core app to be larger since we can't know ahead of time what is needed by each future extension, and therefore can't do tree shaking (whole program optimization).

- **Dynamic patching**. The ability to update the Dart code of an app in the field by downloading a patch (of sorts) and providing it to the Dart VM, probably requiring a restart of the application.

- **Engine**. The C++ (and Java and ObjectiveC and...) side of Flutter. Defined in the `engine` repository. Includes Skia, Dart, and other low-level components. Exposed as `dart:ui` and other Dart libraries to author code.

- **Framework**. The `flutter` library (`packages/flutter` in the main repository) that includes the bulk of the Dart classes exposed by Flutter, such as all the built-in widgets.

- **Hot reload**. During development, causing a running application to update with new logic, without losing any of its state. This contrasts with hot restart because state is maintained despite the code being updated.

- **Hot restart**. During development, causing a running application to rerun from the beginning, using new logic, and with all state reset to initial conditions. This contrasts with a cold restart because it does not require the application to shut down, and contrasts with a hot reload because state is lost in the process.

- **Modular application delivery**. The ability to package a single app into multiple separate archives when compiling it, and download them independently as needed.

- **Skia**. The graphics library used by Flutter.

- **Tool**. The `flutter` program (started from `bin/flutter` on Unix systems, `bin/linux.bat` on Windows) that developers use to drive their work when using Flutter.
