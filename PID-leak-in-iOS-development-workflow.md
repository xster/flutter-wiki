This page addresses a caveat of the on-device iOS hot-reload development workflow.

It may be relevant to you if:
* Your cannot launch more apps on your iOS device after developing a Flutter app for a while.
* You frequent launch *debug-mode* apps from the home screen and develop with `flutter attach`.
* You are seeing a warning mentioning this wiki page.

### Problem

When a *debug-mode* app is installed on an iOS device and launched from within the device, it will reserve a PID (process ID) which is not reclaimed by iOS until the device restarts. Since there is a limit on the number of outstanding PIDs in iOS (~1000), you many not be able to launch apps after some point.

### Solutions

There are three solutions:

* Install a release/profile build. Release and profile builds do not suffer from this issue, and **only** release builds can be deployed to end users via the App Store. They are also more appropriate for testing since their performance is representative of what end users will experience.
* Debug your app from a host workstation/laptop, launching it from within an IDE (such as Xcode or Visual Studio Code) or the `flutter run` command-line tool.
* Restart your iOS device.