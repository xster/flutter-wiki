This page documents interesting or noteworthy changes, including all notable breaking changes, that have been made to Flutter since the last time we released a beta version.

## Changes in v0.2.x (since v0.2.3) - beta 2

### v0.2.5

[#15416](https://github.com/flutter/flutter/pull/15416) removed `package:http` from Flutter and replaced all usages with the `HttpClient` from `dart:io`. If you use `package:http` you must add it as a dependency in your `pubspec.yaml` to continue using it. `createHttpClient()` was also removed after being marked deprecated. To change how the framework creates http clients, you can use [HttpOverrides](https://docs.flutter.io/flutter/dart-io/HttpOverrides-class.html) from `dart:io` to provide your own `createHttpClient()` callback globally or per zone. More details are available [in the announcement](https://groups.google.com/forum/#!topic/flutter-dev/AnqDqgQ6vus).

[#15871](https://github.com/flutter/flutter/pull/15871) changed the default configuration of the `AndroidManifest.xml` created by `flutter create`.  "screenLayout" and "desnity" are now included by default in the configChanges attribute, preventing flutter apps from restarting when these change.

### v0.2.4

[#15565](https://github.com/flutter/flutter/pull/15565) turned Dart 2 mode on by default.  To run in Dart 1 mode, you can still use `--no-preview-dart-2`.

[#15537](https://github.com/flutter/flutter/pull/15537) removed SemanticsSortOrder. From now on traversal sorting is done among sibling nodes only. More details available [in the announcement](https://groups.google.com/forum/#!topic/flutter-dev/iCoLnW31heE).

## Changes in v0.2.3 (since v0.1.5) - beta 1 update

### v0.2.2

[#15484](https://github.com/flutter/flutter/pull/15484) changed the meaning of the `TextFormField` `initialValue` constructor parameter.

The TextFormField initialValue parameter no longer unconditionally initializes the text property of its TextEditingController. If you create a TextFormField and provide a controller, the initialValue must be null, which is now the default. If you're providing a controller you can specify it's initial text value with the TextEditingController text property.

> #### Before
>     new TextFormField(
>       initialValue: 'Hello World',
>       controller: _myTextEditingController,
>     );
>
> #### After
>     new TextFormField(
>       controller: _myTextEditingController ..text = 'Hello World',
>     )
>     // Or more typically:
>     _myTextEditingController = new TextEditingController(
>       text: 'Hello World',
>     );
>     new TextFormField(
>       controller: _myTextEditingController,
>     );

[#15303](https://github.com/flutter/flutter/pull/15303) updated the `showDialog` function to take a builder and deprecated the `widget` parameter.

> #### Before
>     showDialog(context: context, child: new Text('hello'))
>
> #### After
>     showDialog(context: context, builder: (BuildContext context) => new Text('hello'))

[#15265](https://github.com/flutter/flutter/pull/15265) updated `ThemeData` to use the primary color of a `MaterialColor` instead of unconditionally using the 500 shade for light themes.  The color values remain unchanged.

> #### Before
>     expect(widget.color, Colors.blue.shade500) // primary color
>
> #### After
>     expect(widget.color, Colors.blue) // primary color

### v0.2.0

[flutter/engine#4742](https://github.com/flutter/engine/pull/4742) updated assets to be read directly out of the APK on Android. As a result, leading slashes are no longer supported in image asset paths:

> #### Before
>     new Image.asset('/foo/bar.png')
>
> #### After:
>     new Image.asset('foo/bar.png')

### v0.1.9

[#14901](https://github.com/flutter/flutter/pull/14901) A [Slider](https://docs.flutter.io/flutter/material/Slider-class.html) visual update changed the colors, opacities, and the value indicator shape and behavior. It also removed the "`thumbOpenAtMin`" flag from the Slider class, which is no longer needed, and can be emulated by the custom thumb shape support.

## Changes in v0.1.5 (since v0.1.4) - beta 1.1

### v0.1.5

#### Fixed Flutter Gallery build on Android
[#14714](https://github.com/flutter/flutter/pull/14714) fixed the groovy script for the Flutter Gallery, thus fixing [#14912](https://github.com/flutter/flutter/issues/14912).

## Changes in v0.1.4 (since v0.0.20) - beta 1

### v0.1.3

#### flutter tool API changes
[#14702](https://github.com/flutter/flutter/pull/14702) removed the `engineDartVersion` getter from the flutter tool's `Version` class.


### v0.1.1

#### Removed default constructors from dart:ui classes
[flutter/engine#4607](https://github.com/flutter/engine/pull/4607) and [#14601](https://github.com/flutter/flutter/pull/14601) removed default constructors from the following `dart:ui` classes:

* `Codec`
* `FrameInfo`
* `Gradient`
* `Image`
* `Paragraph`
* `Picture`
* `Scene`
* `SemanticsUpdate`
* `Shader`

The default constructors were removed to prevent the creation of uninitialized instances of these classes (and in certain cases to prevent extending these classes). These classes should be instantiated only by the Flutter engine or through named constructors (if provided).


### v0.0.24

#### API changes to material buttons
[#14410](https://github.com/flutter/flutter/pull/14410) contained a breaking API change to `ButtonTheme`:
* The constructors `ButtonTheme()` and `ButtonTheme.bar()` are no longer `const` constructible
* `ButtonTheme.textTheme` is now `ButtonTheme.data.textTheme`
* `ButtonTheme.minWidth` is now `ButtonTheme.data.minWidth`
* `ButtonTheme.height` is now `ButtonTheme.data.height`
* `ButtonTheme.padding` is now `ButtonTheme.data.padding`

[#14410](https://github.com/flutter/flutter/pull/14410) changed the hierarchy of `FlatButton` and `RaisedButton` - they both inherit from `RawMaterialButton` now rather than from `MaterialButton`.

[#14410](https://github.com/flutter/flutter/pull/14410) changed `RaisedButton` to no longer cast a shadow when disabled.


### v0.0.23

#### Copy, Cut, and Paste
[#14343](https://github.com/flutter/flutter/pull/14343) revised how copy, cut, and paste works for EditableText: The abstract class TextSelectionControls has new methods canCopy, canCut, etc. to determine if those actions are available. The TextSelectionDelegate interface now requires an additional method bringIntoView(TextPosition position) to scroll a TextPosition into the visible part of a TextField. Furthermore, that interface is no longer implemented by TextSelectionOverlay. In its place EditableTextState should be used, which implements that interface. See also: [flutter-dev/IHPndyUDy0M](https://groups.google.com/forum/#!topic/flutter-dev/IHPndyUDy0M)

#### Sliver APIs

[#14449](https://github.com/flutter/flutter/pull/14449) replaces the `SliverGridLayout.estimateMaxScrollOffset` method by the `SliverGridLayout.computeMaxScrollOffset` method. This new method must report an accurate value, not just an estimate. This was necessary to fix a bug where a finite `SliverGrid` could not handle being scrolled off the top of the screen (because we had no way to determine how much content it had).

For similar reasons, the `RenderSliverBoxChildManager` interface has a new getter, `childCount`, which must return a non-null value if `createChild` can return null. In practice, it is unusual to implement this interface, so this should have no effect. It is more common to implement the widgets-layer equivalent, `SliverChildDelegate`. This interface already had an `estimatedChildCount` getter. The getter continues to exist, though its semantics have been adjusted a little to require that the returned value be accurate if the `build` method on the delegate ever returns null.


### v0.0.21

#### InputDecorator layout has changed
[#13734](https://github.com/flutter/flutter/pull/13734), [#14055](https://github.com/flutter/flutter/pull/14055), [#14177](https://github.com/flutter/flutter/pull/14177) substantially revised the InputDecorator et al. widgets. The layout of the input decorator's parts has changed a little, which means that the internal layout of text fields has changed as well. Tests that depend on the internal geometry of text fields will need to be updated.

In addition, `hideDivider: true` must be replaced by the new `border: InputBorder.none`. This is part of our making it easier to customize how inputs are rendered; you can now also provide a custom InputBorder subclass if you have particularly novel desires for your input decoration.

#### Remove support for big integers in platform channels
[#4528](https://github.com/flutter/engine/pull/4528), [#14011](https://github.com/flutter/flutter/pull/14011) deprecate support for big integers in the standard platform channel message/method codecs, to be made unavailable following a four week grace period. This change is a consequence of the transition to Dart
2.0 where the `int` type is no longer unlimited size.

#### Typo fixed in Flutter Engine Android API
[#4487](https://github.com/flutter/engine/pull/4487) replaces all uses of the `RequestPermissionResult` callback concept in `io.flutter.plugin.common.PluginRegistry` with `RequestPermissionsResult`, adding a missing `s` to align with the corresponding Android SDK concept.

The old API has been deprecated and will be made unavailable in a later release. There will be a grace period of at least four weeks between the release that introduces the deprecation and the release that makes the old API unavailable.