For the framework
-----------------

Dart tests are written using the `flutter_test` package's API,
named with the suffix `_test.dart`, and placed inside the
`test/` subdirectory of the package under test.

We support several kinds of tests:

* Unit tests, e.g. using [`flutter_test`](https://master-docs-flutter-io.firebaseapp.com/flutter/flutter_test/flutter_test-library.html). See below.

* Unit tests that use golden-file testing, comparing pixels.
  See [[Writing a golden-file test for package:flutter]].

* End-to-end tests, e.g. using [`flutter_driver`](https://master-docs-flutter-io.firebaseapp.com/flutter/flutter_driver/flutter_driver-library.html) and our [device lab](https://github.com/flutter/flutter/blob/master/dev/devicelab/README.md).

Our bots run on our [test and build infrastructure](https://github.com/flutter/flutter/blob/master/dev/bots/README.md).

## Running unit tests

Flutter tests use the `flutter_test` package ([source](https://github.com/flutter/flutter/tree/master/packages/flutter_test), [API documentation](https://master-docs-flutter-io.firebaseapp.com/flutter/flutter_test/flutter_test-library.html)),
which provides flutter-specific extensions on top of the [Dart `test` package](https://pub.dartlang.org/packages/test).

To automatically find all files named `*_test.dart` inside a package's `test/` subdirectory, and
run them inside the headless flutter shell as a test, use the `flutter test` command, e.g:

 * `cd examples/flutter_gallery`
 * `flutter test`

Individual tests can also be run directly, e.g.: `flutter test lib/my_app_test.dart`

You can view these tests on a device by running them directly using `flutter run`.
For tests inside the `packages/flutter` directory, you will need to copy them to
(or symlink to them from) the `test/` directory of an actual app (e.g. the flutter
gallery), since the `flutter` package itself is not set up to execute as an
application (which is necessary to use `flutter run` with a test).

Unit tests run with `flutter test` run inside a headless flutter shell on your workstation,
you won't see any UI. You can use `print` to generate console output or you can interact
with the Dart VM via the Dart Observatory at [http://localhost:8181/](http://localhost:8181/).

To debug tests in Observatory, use the `--start-paused` option to start the test in a
paused state and wait for connection from a debugger. This option lets you set breakpoints
before the test runs.

To run analysis and all the tests for the entire Flutter repository, the same way that Cirrus
runs them, run `dart dev/bots/test.dart` and `dart dev/bots/analyze.dart`.

If you've built your own flutter engine (see [[Setting up the Engine development environment]]), you
can pass `--local-engine` to change what flutter shell `flutter test` uses. For example,
if you built an engine in the `out/host_debug_unopt` directory, you can pass
`--local-engine=host_debug_unopt` to run the tests in that engine.

To learn how to see how well tested the codebase is, see [[Test coverage for package:flutter]].