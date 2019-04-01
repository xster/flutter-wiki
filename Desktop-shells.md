Work is ongoing to extend Flutter to support desktop as a target environment, allowing developers to create macOS, Windows, and Linux applications with Flutter. On the long run, this effort will create lead to a fully integrated solution where `flutter create`, `flutter run`, and `flutter build` work for desktop platforms as they do for mobile platforms, but currently this effort is still under way.

## Building for macOS

This is the first platform we are targeting (for various reasons, including that it's quite close to iOS, which we already support).

You can see our current macOS embedding here: https://github.com/flutter/engine/tree/master/shell/platform/darwin/macos

<!-- insert instructions here -->

## Building for Windows.

We have not yet built a shell for Windows, though that is high on our list.

## Building for Linux.

We have not yet built a shell for Linux. We would like to create a library that lets you embed Flutter regardless of whether you're using GTK+, Qt, wxWidgets, Motif, or another arbitrary toolkit for other parts of your application, but have not yet determined a good way to do that.
