This is a list of all the [dev branch commits](https://github.com/flutter/flutter/releases) that are considered "bad" and should not be released to a more stable channel. See [[Bad Build Identification]] for details on how to identify builds that should be listed here. See also the [[Changelog]].

**If you add a build to this list and master has not yet been fixed, please file a TODAY bug and link to it from here as well.**

## all versions to _present_:
 * Android and IOS Add-to-App apps will crash if a view is presented, dismissed, presented again, and any user interaction occurs [#31139](https://github.com/flutter/flutter/issues/31139)

## v1.12.4 - v1.12.11
 * Flutter framework shows assertion error on showing and dismissing a Flutter Activity or ViewController [#45575](https://github.com/flutter/flutter/issues/45575)

## v1.10.15 - v1.12.10
 * ActivityPluginBinding.getLifecycle unwrapping doesn't work because transitive Android dependencies weren't brought in [#45188](https://github.com/flutter/flutter/issues/45188)

## v1.12.7 - v1.12.12:
 * An mdns lookup failure during tool attach will crash the tool instead of falling back on log scraping [#45468](https://github.com/flutter/flutter/issues/45468)

## v1.12.3 - v1.12.4:
 * iOS apps are rejected during App Store submission [skia#9657](https://bugs.chromium.org/p/skia/issues/detail?id=9657)
 * Images don't display on iOS [#45098](https://github.com/flutter/flutter/issues/45098)

## v1.10.15-pre.396 - v1.10.16
 * Android plugins using auto registration doesn't work [#44161](https://github.com/flutter/flutter/issues/44161)

## v1.9.2 - v1.10.14
 * iOS Apps built with Bitcode will crash on launch [#42458](https://github.com/flutter/flutter/issues/42458)

## v1.10.8
 * microbenchmarks_stock_build_iteration regression [#41919](https://github.com/flutter/flutter/issues/41919)

## v1.10.4 - v1.10.6
 * iOS Gesture detector is not working well [#40863](https://github.com/flutter/flutter/issues/40863)

## v1.9.4 - v1.10.4

* WidgetsFlutterBinding.ensureInitialized() takes down splash screen too early [#39494](https://github.com/flutter/flutter/issues/39494)

## v1.9.2 - v1.9.7

* Soft keyboard can become unresponsive [#39182](https://github.com/flutter/flutter/issues/39182), [#39047](https://github.com/flutter/flutter/issues/39047)

## v1.9.0
* Platform views do not display on iPhones [#38735](https://github.com/flutter/flutter/issues/38735)

## v1.7.6 - v1.7.11
* WebView is sized (0,0) for some pages on Android [#35291](https://github.com/flutter/flutter/issues/35291)

## v1.7.6 - v1.7.7
* `flutter run` yielded a significant amount of log spam [#34876](https://github.com/flutter/flutter/issues/34876)

## v1.7.0 - v1.7.5
* Applications built with `flutter build appbundle` and `flutter build apk` will crash in add-to-app scenarios (https://github.com/flutter/flutter/issues/34308).

## v1.7.1
* Any usage of platform views on Android results in a "reply already submitted" error (https://github.com/flutter/flutter/issues/33866).

## v1.6.1 - v1.6.7
* Android builds get stuck in the splash screen after deploying an APK from an app bundle (generated using `flutter build appbundle`)

## v1.5.8 - v1.6.2
* The analyzer suffers from a serious (>10%) performance regression. (https://github.com/flutter/flutter/issues/32320)

## v1.5.3 - v1.6.0
* When an iOS back swipe gesture is dropped exactly back at the starting edge of the screen, subsequent back swipes stop working (https://github.com/flutter/flutter/issues/31471)

## v1.5.2 - 1.5.8

* (minor) Engine crashes when confronted with some unconstrained layouts (https://github.com/flutter/flutter/issues/31650)

## v1.3.10 - 1.5.8

* Selection handles don't appear on iOS (https://github.com/flutter/flutter/issues/31287)

## v1.4.8 - v1.4.11
* Opacity layer's child transform matrix is not initialized (started from [this engine roll](https://github.com/flutter/flutter/commit/daeaffc18838166555b480e928b3cdd8e4a5d937)), tracking issue: [#30586](https://github.com/flutter/flutter/issues/30586). Fixed in https://github.com/flutter/engine/pull/8467 (rolled in https://github.com/flutter/flutter/commit/e09fc3574a8e4d243668c8f7b703378eca0bab24).

## v1.4.0 - v1.4.8
* "Detected problems with API compatibility" dialog shows on Android P (started at https://github.com/flutter/flutter/commit/697a2a828fce98ec5cffd9dd2fdb34ccd200e38a), tracking issue: [#30395](https://github.com/flutter/flutter/issues/30395). Fix was rolled at https://github.com/flutter/flutter/commit/d639883c5b72e6166c8241ab57114ea529f516a7.
* Error message for setting `PaintingBinding.shaderWarmup` too late [#30145](https://github.com/flutter/flutter/issues/30145) may cause diffs in some (racy?) driver tests.

## v1.3.14
* `flutter upgrade` will stash changes in the user's project folder.

## v1.3.9 - v.1.3.13
* Android Emulator renders a white screen on first frame or after resuming from background until interaction with app or hot reload/restart (https://github.com/flutter/flutter/issues/29679)

## v1.3.9
* Android crash on FlutterView.getAccessibilityNodeProvider due to NPE
(https://github.com/flutter/flutter/issues/29203)

## v1.2.2
* Android crash on startup in FlutterView.sendLocalesToDart on API < 24 (https://github.com/flutter/flutter/issues/28321)

## v1.1.1 - v1.1.2
* Bad vector graphics caused by a Skia regression (https://github.com/flutter/flutter/issues/25426)

## v0.11.6 - v0.11.9
* Suffers from splash screen not loading propertly for iOS apps (https://github.com/flutter/flutter/issues/24563)

## v0.10.3 - v0.11.6
* Suffers from hot reload flakiness (https://github.com/flutter/flutter/issues/24400)

## v0.10.0 - v0.11.1
* Workflows that require customizing the underlying `android` or `ios` apps and newly created apps using `cupertino_icons` are broken by [#22565](https://github.com/flutter/flutter/pull/22565).

## v0.10.0
* The colorScheme of ButtonTheme and minWidth of MaterialButton are not properly handled. See [#22789](https://github.com/flutter/flutter/issues/22789), [flutter/flutter#22880](https://github.com/flutter/flutter/pull/22880), and [flutter/flutter#22919](https://github.com/flutter/flutter/pull/22919)

## v0.9.6
* Copy/pasting broken on iOS in material apps, see [#22624](https://github.com/flutter/flutter/issues/22624).

## v0.9.5 - v0.9.6
* Upload to iOS app store is broken, see [#22306](https://github.com/flutter/flutter/issues/22306).

## v0.5.2 - v0.5.6
* Dart VM crashes on certain eval calls, see [#18573](https://github.com/flutter/flutter/issues/18573).

## v0.4.2

* Windows-specific bug in which android_arm release/profile snapshots build with hardfp when they should use softfp, see [#17387](https://github.com/flutter/flutter/issues/17387).

## v0.3.1 - ???

* Accessibility on iOS is broken, see [#16904](https://github.com/flutter/flutter/issues/16904).

## v0.2.10 - v0.2.11, v0.3.0

* iOS release binary sizes are bloated with image encoding support. The fix required an API update to `Image.toByteData()`.  Since the API containing the image encoding was immediately changed, we don't want to release the bad API. [#16537](https://github.com/flutter/flutter/issues/16537)

## v0.2.9

* Android license status shows as "unknown" with certain Android SDK versions. [#16228](https://github.com/flutter/flutter/issues/16228)

## v0.2.4 - v0.2.6

* Flutter tool invocation from xcode_backend.sh never exits. [#15944](https://github.com/flutter/flutter/issues/15944)

## v0.2.5

* Tooltip of FAB doesn't end up as semantics label of FAB [#16063](https://github.com/flutter/flutter/issues/16063)

## v0.2.4

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
