The `flutter` command-line tool is how developers (or IDEs on behalf of developers) interact
with Flutter.

`flutter --help` lists the developer-facing commands that `flutter` supports.

`flutter --help --verbose` lists _all_ the commands that `flutter` supports, in particular, it also lists the
features that are of use to Flutter contributors.

These include:

* `flutter update-packages`, which downloads all the Dart dependencies for all
  Dart packages in the Flutter repository.
* `flutter analyze --flutter-repo`, as described on [[Using the Dart analyzer]].

When contributing to Flutter, use `git pull --rebase` or `git rebase upstream/master`
rather than `flutter upgrade`.

The `flutter` tool itself is built when you run `flutter` for the first time and each time
you run `git pull --rebase` (or `flutter upgrade`, or anything that changes the current commit).


## Making changes to the `flutter` tool

If you want to alter and re-test the tool's behavior itself, locally commit your tool change
in git and the tool will be rebuilt from Dart sources in `packages/flutter_tools` the next
time you run `flutter`. Alternatively, delete the `bin/cache/flutter_tools.snapshot` file.
Doing so will force a rebuild of the tool from your local sources the next time you run `flutter`.

The `flutter_tools` tests run inside the Dart command line VM rather than in the
flutter shell. To run the tests, navigate to `packages/flutter_tools` and run:

```shell                                                                                                                       
../../bin/cache/dart-sdk/bin/pub run test
```                                                                                                                            

The pre-built flutter tool runs in release mode with the observatory off by default.
To enable debugging mode and the observatory on the `flutter` tool, uncomment the
`FLUTTER_TOOL_ARGS` line in the `bin/flutter` shell script.


## Adding, removing, or making changes to Dart dependencies

Once you've edited a `pubspec.yaml` file in the Flutter repository to change a package's dependencies,
run `flutter update-packages --force-upgrade` to resynchronize all the `pubspec.yaml` files.
This does a full cross-package version solve for the entire repository.

If you need to pin a particular version, edit the table at the top of the `update_packages.dart` file.


## Using a locally-built engine with the `flutter` tool

### General

To allow the tool to be used with a locally-built engine, the `flutter` tool accepts two
global parameters: `local-engine-src-path`, which specifies the path to your engine repository,
and  `local-engine`, which specifies which build of the engine to use.

A typical invocation would be: `--local-engine-src-path /path/to/engine/src --local-engine=android_debug_unopt`.

If your engine is in a directory called `engine` that is a peer to the framework repository's `flutter` directory, then you can omit `--local-engine-src-path` and only specify `--local-engine`.

You can also set the environment variable `$FLUTTER_ENGINE` instead of specifying `--local-engine-src-path`.

The `--local-engine` should specify the build of the engine to use, e.g. a profile build for Android, a debug build for Android, or whatever. It must match the other arguments provided to the tool, e.g. don't use the `android_debug_unopt` build when you specify `--release`, since the Debug build expects to compile and run Dart code in a JIT environment, while `--release` implies a Release build which uses AOT compilation.

Additionally if you've modified the Dart sources in your engine,
you will need to add a `dependency_overrides` section to point to your
modified `package:sky_engine` and `package:sky_services` to the
`pubspec.yaml` for the flutter app you're using the custom engine
with. A typical example would be:

```yaml
dependency_overrides:
  sky_engine:
    path: /path/to/flutter/engine/out/host_debug/gen/dart-pkg/sky_engine
  sky_services:
    path: /path/to/flutter/engine/out/host_debug/gen/dart-pkg/sky_services
```

Replace `host_debug` with the actual build that you want to use (similar to `--local-engine`, but typically
a host build rather than a device build).

If you do this, you can omit `--local-engine-src-path` and not bother to set `$FLUTTER_ENGINE`, as
the `flutter` tool will use these paths to determine the engine also! The tool tries really hard to
figure out where your local build of the engine is if you specify `--locale-engine`.
