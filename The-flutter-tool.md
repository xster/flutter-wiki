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