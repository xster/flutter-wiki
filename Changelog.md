This page documents interesting or noteworthy changes, including all notable breaking changes, that have been made to Flutter since the last time we released a beta version.

## Changes since 0.0.22
[#14343](https://github.com/flutter/flutter/pull/14343) revised how copy, cut, and paste works for EditableText: The abstract class TextSelectionControls now has methods canCopy, canCut, etc. to determine if those actions are available. The TextSelectionDelegate interface now requires an additional method bringIntoView(TextPosition position) to scroll a TextPosition into the visible part of a TextField. Furthermore, that interface is no longer implemented by TextSelectionOverlay. In its place EditableTextState should be used, which now implements that interface.

## Changes since 0.0.20

### InputDecorator layout has changed
[#13734](https://github.com/flutter/flutter/pull/13734), [#14055](https://github.com/flutter/flutter/pull/14055), [#14177](https://github.com/flutter/flutter/pull/14177) substantially revised the InputDecorator et al. widgets. The layout of the input decorator's parts has changed a little, which means that the internal layout of text fields has changed as well. Tests that depend on the internal geometry of text fields will need to be updated.

In addition, `hideDivider: true` must be replaced by the new `border: InputBorder.none`. This is part of our making it easier to customize how inputs are rendered; you can now also provide a custom InputBorder subclass if you have particularly novel desires for your input decoration.

### Remove support for big integers in platform channels
[#4528](https://github.com/flutter/engine/pull/4528), [#14011](https://github.com/flutter/flutter/pull/14011) deprecate support for big integers in the standard platform channel message/method codecs, to be made unavailable following a four week grace period. This change is a consequence of the transition to Dart
2.0 where the `int` type is no longer unlimited size.

### Typo fixed in Flutter Engine Android API
[#4487](https://github.com/flutter/engine/pull/4487) replaces all uses of the `RequestPermissionResult` callback concept in `io.flutter.plugin.common.PluginRegistry` with `RequestPermissionsResult`, adding a missing `s` to align with the corresponding Android SDK concept.

The old API has been deprecated and will be made unavailable in a later release. There will be a grace period of at least four weeks between the release that introduces the deprecation and the release that makes the old API unavailable.