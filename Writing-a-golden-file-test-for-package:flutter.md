_(This page is referenced by comments in the Flutter codebase.)_

Golden file tests for `package:flutter` use [Flutter Gold](https://flutter-gold.skia.org/?query=source_type%3Dflutter) for baseline and version management of golden files. This allows for golden file testing on Linux, Windows, and MacOS. If you have questions about [Flutter Gold](https://flutter-gold.skia.org/?query=source_type%3Dflutter), cc **@Piinks** on your pull request.

## Creating a New Golden File Test

Write your test as a normal test, using `testWidgets` and `await tester.pumpWidget` and so on.

Put a `RepaintBoundary` widget around the part of the subtree that you want to verify. If you don't, the output will be a 2400x1800 image, since the tests by default use an 800x600 viewport with a device pixel ratio of 3.0. If you would like to further control the image size, put a `SizedBox` around your `RepaintBoundary` to set constraints.

Add an expectation along the following lines:

```dart
  await expectLater(
    find.byType(RepaintBoundary),
    matchesGoldenFile( 'test_name.subtest.subfile.png'),
  );
```

The argument to `matchesGoldenFile` is the filename for the screen shot. The part up to the first dot should exactly match the test filename (e.g. if your test is `widgets/foo_bar_test.dart`, use `foo_bar`). The `subtest` part should be unique to this `testWidgets` entry, and the part after that should be unique within the `testWidgets` entry. This allows each file to have multiple `testWidgets` tests each with their own namespace for the images, and then allows for disambiguation within each test in case there are multiple screen shots per test. 

Golden tests may be executed locally on Linux, MacOS, and Windows platforms. All reference images can be found at [Flutter Gold baselines](https://flutter-gold.skia.org/list?fdiffmax=-1&fref=false&frgbamax=255&frgbamin=0&head=true&include=false&limit=50&master=false&match=name&metric=combined&neg=false&new_clstore=true&offset=0&pos=true&query=source_type%3Dflutter&sort=desc&unt=true). Some tests may have multiple golden masters for a given test, to account for rendering differences across platforms. The parameter set for each image is listed in each image digest to differentiate renderings. 

Once you have written your test, run `flutter test --update-goldens test/foo/bar_test.dart` in the `flutter` package directory (where the filename is the relative path to your new test). This will update the images in `bin/cache/pkg/skia_goldens/packages/flutter/test/`; the directories below that will match the hierarchy of the directories in the `test` directory of the `flutter` package. Verify that the images are what you expect; update your test and repeat this step until you are happy with the image.

When running `flutter test` without the `--update-goldens` flag, your test will pass, as it does not yet have a baseline for comparison on the [Flutter Gold](https://flutter-gold.skia.org/?query=source_type%3Dflutter) dashboard. The test will be recognized as new, and provide output for validation.

When you are happy with your image, you are ready to submit your PR for review. The reviewer should also verify your golden image(s), so make sure to include the golden(s) in your PR description. During pre-submit testing, the new golden file test is further recognized as a new test, and will pass.

When your pull request is merged (:tada:), and post-submit testing has been completed (the status of which can be seen on the [Flutter Build Dashboard](https://flutter-dashboard.appspot.com/build.html)), your change will need to be triaged. Your change and the affected files will be compiled into a digest on the [Flutter Gold](https://flutter-gold.skia.org/?query=source_type%3Dflutter) dashboard for this purpose. 

Review the digest and the images that were generated, making sure they look as expected. Currently, we generate images for Linux, Mac and Windows platforms. It is common for there to be slight differences between them. Click the checkmark to approve the change, completing triage. 

And that’s it! Your new golden file will be checked in as the baseline for your new test.

*Triage permission is currently restricted to googlers, with plans to extend to members of flutter-hackers. For more information, see [Contributor Access](https://github.com/flutter/flutter/wiki/Contributor-access).* 

## Updating a Golden File Test

If renderings change, then the golden baseline in [Flutter Gold](https://flutter-gold.skia.org/?query=source_type%3Dflutter) will need to be updated.

When developing your change, local testing will produce a failure along with visual diff output. This visual diff will be generated using the golden baseline from [Flutter Gold](https://flutter-gold.skia.org/?query=source_type%3Dflutter) that matches the current paramset of your testing environment (currently based on platform). This allows for quick iterations and validation of your change. Locally, this test will not pass until the new baseline has been checked in.

When you are happy with your golden change, you are ready to submit your PR for review. The reviewer should also verify your golden image(s), so make sure to include the golden changes you have made in your PR description. During pre-submit testing, the updated golden file test will only pass after activating an ignore for your change. 

An ignore can be put in place for the affected test(s) and pull request through the [Flutter Gold dashboard ignores page](https://flutter-gold.skia.org/ignores). Click the `+` button to add a new ignore. You will need to specify:

- a `duration` of time it will be active for. Be conservative in choosing a duration (e.g. 2 months). Other pull requests will be unaffected by this ignore. Your ignore should be active long enough for post-submit checks to finish once it has landed, and triage to be completed.
- a link to your pull request that is making the change under `note`.
- the tests affected by your change (you may select multiple tests for the `filter`).

Once the ignore has been activated, the specified tests for the specified pull request will pass, allowing you to land your change.

Changes such as these constitute a breaking change, and should follow [Handling Breaking Changes](https://github.com/flutter/flutter/wiki/Tree-hygiene#handling-breaking-changes). This includes marking your PR with the `severe: API break` and `will affect goldens` labels, as well as updating the [Changelog](https://github.com/flutter/flutter/wiki/Changelog). You may notice the **FlutterGitHubBot** apply labels and remind you to complete triage on Flutter Gold after merging a PR that includes a golden file test. 

When your pull request is merged (:tada:), and post-submit testing has been completed (the status of which can be seen on the [Flutter Build Dashboard](https://flutter-dashboard.appspot.com/build.html)), your change will need to be triaged. Your change and the affected files will be compiled into a digest on the [Flutter Gold dashboard ignores page](https://flutter-gold.skia.org/ignores) for this purpose.

Revisit your ignore to review the digest and the images that were generated, making sure they look as expected. Currently, we generate images for Linux, Mac and Windows platforms. It is common for there to be slight differences between them. Click the checkmark to approve the change and complete triage. 

And that’s it! The baseline for your golden file test(s) will be updated with your change.

*Triage permission is currently restricted to googlers, with plans to extend to members of flutter-hackers. For more information, see [Contributor Access](https://github.com/flutter/flutter/wiki/Contributor-access).* 
