This page documents interesting or noteworthy changes, including all notable breaking changes, that have been made to Flutter since the last time we released a beta version.

## Changes since 0.0.20

### InputDecorator layout has changed
[#13734](https://github.com/flutter/flutter/pull/13734), [#14055](https://github.com/flutter/flutter/pull/14055), [#14177](https://github.com/flutter/flutter/pull/14177) substantially revised the InputDecorator et al. widgets. The layout of the input decorator's parts has changed a little, which means that the internal layout of text fields has changed as well. Tests that depend on the internal geometry of text fields will need to be updated.

### Remove support for big integers in platform channels
[#4528](https://github.com/flutter/engine/pull/4528), [#14011](https://github.com/flutter/flutter/pull/14011) deprecate support for big integers in the standard platform channel message/method codecs, to be made unavailable following a four week grace period. This change is a consequence of the transition to Dart
2.0 where the `int` type is no longer unlimited size.

### Typo fixed in Flutter Engine Android API
[#4487](https://github.com/flutter/engine/pull/4487) replaces all uses of the `RequestPermissionResult` callback concept in `io.flutter.plugin.common.PluginRegistry` with `RequestPermissionsResult`, adding a missing `s` to align with the corresponding Android SDK concept.

The old API has been deprecated and will be made unavailable in a later release. There will be a grace period of at least four weeks between the release that introduces the deprecation and the release that makes the old API unavailable.