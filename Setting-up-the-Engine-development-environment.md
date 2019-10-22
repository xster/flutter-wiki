_If you've already built the engine and have the configuration set up but merely need a refresher on
actually compiling the code, see [[Compiling the engine]]._

Make sure you have the following dependencies available:

 * Linux, macOS, or Windows.

     * Linux supports cross-compiling artifacts for Android, but not iOS.

     * macOS supports cross-compiling artifacts for Android and iOS.

     * Windows doesn't support cross-compiling artifacts for either Android or iOS.

 * git (used for source version control).

 * An IDE. [Android Studio with the Flutter plugin](https://flutter.io/using-ide/) is
   our flagship IDE. You can use whatever IDE you feel most comfortable with. See also
   the section at the bottom of this page for advice on setting up autocomplete with
   a tool called `cquery`.

 * An ssh client (used to authenticate with GitHub).

 * Chromium's
   [depot_tools](http://commondatastorage.googleapis.com/chrome-infra-docs/flat/depot_tools/docs/html/depot_tools_tutorial.html#_setting_up)
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

12. If you're on Linux, run: `sudo ./flutter/build/install-build-deps-linux-desktop.sh`

13. If you're on Mac, install Oracle's Java JDK, version 1.8 or later.

14. If you're on Mac, install `ant`: `brew install ant`

15. If you're on Windows, install Visual Studio (non-Google developers only).

16. If you're planning on working on the
    [buildroot](https://github.com/flutter/buildroot) repository as
    well, and have a local checkout of that repository, run the
    following commands in the `src` directory to update your git
    remotes accordingly:

    ```bash
    git remote rename origin upstream
    git remote add origin git@github.com:<your_name_here>/buildroot.git
    ```
17. Additionally if you've modified dart sources in `flutter/engine`, you'll
    need to add a `dependency_overrides` section to point to your modified
    `package:sky_engine` and `package:sky_services` to the `pubspec.yaml`
    for the flutter app you're using the custom engine with.  A typical
    example would be:

    ```	
    dependency_overrides:	
      sky_engine:	
        path: /path/to/flutter/engine/src/out/host_debug/gen/dart-pkg/sky_engine	
      sky_services:	
        path: /path/to/flutter/engine/src/out/host_debug/gen/dart-pkg/sky_services	
    ```	
    Depending on the platform you choose below, you will need to replace `host_debug` with the appropriate 
    directory.

Next steps:

 * [[Compiling the engine]] explains how to actually get builds, now that you have the code.
 * [[The flutter tool]] has a section explaining how to use custom engine builds.


## Editor autocomplete support

### XCode

On Mac, you can simply use Xcode (e.g., `open out/host_debug_unopt/products.xcodeproj`).

### VSCode with C/C++ Intellisense

VSCode can also provide some IDE features using the [C/C++ extension](https://marketplace.visualstudio.com/items?itemName=ms-vscode.cpptools). It will provide basic support with some missing header and incorrect jump to definitions on installation, without any configuration.

For better support, Intellisense also supports our `compile_commands.json`. Either symlink `src/out/compile_commands.json` to the project root at `src/flutter` OR provide an absolute path to it in the `c_cpp_properties.json` config file. See ["compile commands" in the c_cpp_properties.json reference](https://code.visualstudio.com/docs/cpp/c-cpp-properties-schema-reference).

For adding IDE support to the Java code in the engine with VSCode, see ["Using VSCode as an IDE for the Android Embedding"](https://github.com/flutter/flutter/wiki/Setting-up-the-Engine-development-environment#using-vscode-as-an-ide-for-the-android-embedding).

### CQuery (multiple editors)

Alternatively, [cquery](https://github.com/cquery-project/cquery) is a highly scalable C/C++/Objective-C language server that supports IDE features like go-to-definition, call hierarchy, autocomplete, find reference etc that works reasonably well with our engine repo. 

It [supports](https://github.com/cquery-project/cquery/wiki/Editor-configuration) editors like VSCode, emacs, vim etc. 

To set up:
1. Install cquery
    1. `brew install --HEAD cquery` on osx or
    1. [Build from source](https://github.com/cquery-project/cquery/wiki/Getting-started)
1. Generate compile_commands.json which our GN tool already does such as via `src/flutter/tools/gn --ios --unoptimized` 
1. Install an editor extension such as [VSCode-cquery](https://marketplace.visualstudio.com/items?itemName=cquery-project.cquery)
    1. VSCode-query requires the compile_commands.json to be at the project root. Copy or symlink `src/out/compile_commands.json` to `src/` or `src/flutter`.
    1. Follow [Setting up the extension](https://github.com/cquery-project/cquery/wiki/Visual-Studio-Code#setting-up-the-extension) to configure VSCode-query.

![](https://media.giphy.com/media/xjIrToRDVvMPvjkBcl/giphy.gif)

## VSCode Additional Useful Configuration

1. Create [snippets](https://code.visualstudio.com/docs/editor/userdefinedsnippets) for header files with [this configuration](https://github.com/chromium/chromium/blob/master/tools/vscode/settings.json5). This will let you use `hdr` keyboard macro to create the boiler plate header code. Also consider some of [these settings](https://github.com/chromium/chromium/blob/master/tools/vscode/settings.json5) and [more tips](https://chromium.googlesource.com/chromium/src/+show/lkgr/docs/vscode.md).

2. To format GN files on save, [consider using this extension](https://marketplace.visualstudio.com/items?itemName=persidskiy.vscode-gnformat).

### Using VSCode as an IDE for the Android Embedding

Create a `.classpath` file in `engine/src/flutter/platform/android` so that the IDE can understand the project structure. [Here's an example file](https://gist.github.com/mklim/b0d0c9c5084d5abb9e012c051a2cb5ff) that worked as of the time of this writing.

* It should contain links to all of the engine's JAR dependencies in `third_party`, including the Android SDK.
* It should also list the `test/` subdirectory as a `src` directory to fix incorrect package errors on the unit test files.