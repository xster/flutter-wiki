This is a list of all the dev branch commits that are considered "bad" and should not be released to a more stable channel. See [[Bad Build Identification]] for details on how to identify builds that should be listed here.

## v0.2.5

* Tooltip of FAB doesn't end up as semantics label of FAB [#16063](https://github.com/flutter/flutter/issues/16063)

## v0.2.4

* Flutter tool invocation from xcode_backend.sh never exists. [#15944](https://github.com/flutter/flutter/issues/15944)
* Crash when doing full restart that spawns an isolate. [#15894](https://github.com/flutter/flutter/issues/15894)
* Default `TabBar` `indicatorColor` doesn't contrast with its background. [#15958](https://github.com/flutter/flutter/issues/15958)

## v0.1.6 - v0.1.8

* `flutter test` loads test forever when run with `--preview-dart-2`. [#14931](https://github.com/flutter/flutter/issues/14931)

## v0.0.23 - v0.1.4

* Android Gallery gradle build fails on tagged commits without the `-pre.x` suffix. [#14912](https://github.com/flutter/flutter/issues/14912)

## v0.1.1 - v0.1.2

* Contain Dart SDK version `2.0.0-dev.23.0`, which suffers from [dart-lang/sdk#32172](https://github.com/dart-lang/sdk/issues/32172) and manifests to the user as [#14696](https://github.com/flutter/flutter/issues/14696).
* Suffer from [#14751](https://github.com/flutter/flutter/issues/14751).  They don't yet contain the change that unified the engine Dart SDK and the flutter tool Dart SDK, but these releases are prone to a different manifestation of the same problem with pub's version resolver checking the flutter tool's Dart SDK version and then running code against the engine Dart SDK version. 

## v0.0.22

* Anecdotally, we've seen people complain more when using v0.0.22 than v0.0.21.
