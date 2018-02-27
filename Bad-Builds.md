This is a list of all the dev branch commits that are considered "bad" and should not be released to a more stable channel:

## v0.0.23 - v0.1.4
* Android Gallery gradle build fails on tagged commits without the -pre.x suffix. [#14912](https://github.com/flutter/flutter/issues/14912)

## v0.1.1 - v0.1.2

* Builds containing a Dart SDK version that suffers from [dart-lang/sdk#32172](https://github.com/dart-lang/sdk/issues/32172) (2.0.0-dev.23.0) manifest as [#14696](https://github.com/flutter/flutter/issues/14696).
* These releases suffer from [#14751](https://github.com/flutter/flutter/issues/14751).  They don't yet contain the change that unified the engine Dart SDK and the flutter tool Dart SDK, but these releases are prone to a different manifestation of the same problem with pub's version resolver checking the flutter tool's Dart SDK version and then running code against the engine Dart SDK version. 

## v0.0.22

* Anecdotally, we've seen people complain more when using v0.0.22 than v0.0.21.
