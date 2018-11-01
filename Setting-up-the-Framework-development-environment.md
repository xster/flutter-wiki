
Getting the code and configuring your environment
-------------------------------------------------

Make sure you have the following dependencies available:

 * Linux, Mac OS X, or Windows.
 * git (used for source version control).
 * An IDE. [Android Studio with the Flutter plugin](https://flutter.io/using-ide/) is
   our flagship IDE. You can use whatever IDE you feel most comfortable with.
 * An ssh client (used to authenticate with GitHub).
 * Python (used by some of our tools).
 * The Android platform tools.

   You can install this using one of the following commands:
   - Mac: `brew cask install android-platform-tools`
   - Linux: `sudo apt-get install android-tools-adb`

   If you're
   [also working on the Flutter engine](https://github.com/flutter/flutter/wiki/Setting-up-the-Engine-development-environment),
   you can use the copy of the Android platform tools in
   `.../engine/src/third_party/android_tools/sdk/platform-tools`.

Run the following steps to set up your environment:

 * Ensure that `adb` (from the Android platform tools) is in your path (e.g.,
   that `which adb` prints sensible output).
 * Fork `https://github.com/flutter/flutter` into your own GitHub account. If
   you already have a fork, and are now installing a development environment on
   a new machine, make sure you've updated your fork so that you don't use stale
   configuration options from long ago.
 * If you haven't configured your machine with an SSH key that's known to github then
   follow the directions here: https://help.github.com/articles/generating-ssh-keys/.
 * `git clone git@github.com:<your_name_here>/flutter.git`
 * `cd flutter`
 * `git remote add upstream git@github.com:flutter/flutter.git` (So that you
   fetch from the master repository, not your clone, when running `git fetch`
   et al.)
 * Add this repository's `bin` directory to your path. That will let you use the
   `flutter` command in this directory more easily.
 * Run `flutter update-packages` This will fetch all the Dart packages that
   Flutter depends on. (You can replicate mwost of what this script does by running
   `pub get` in each directory that contains a `pubspec.yaml` file, which is rather
   tedious, hence the script.)
 * If you plan on using IntelliJ as your IDE, then also run
   `flutter ide-config --overwrite` to create all of the IntelliJ configuration
   files so you can open the main flutter directory as a project and run examples
   from within the IDE.




See https://github.com/flutter/flutter/blob/master/CONTRIBUTING.md