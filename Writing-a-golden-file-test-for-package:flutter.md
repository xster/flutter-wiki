_(This page is referenced by comments in the Flutter codebase.)_

Golden file tests for `package:flutter` use [Flutter Gold](https://flutter-gold.skia.org/?query=source_type%3Dflutter) for baseline and version management of golden files. This allows for golden file testing on Linux, Windows, MacOS and Web across two CI environments. If you have questions about [Flutter Gold](https://flutter-gold.skia.org/?query=source_type%3Dflutter), cc **@Piinks** on your pull request.

## Index
- [Creating a New Golden File Test](https://github.com/flutter/flutter/wiki/Writing-a-golden-file-test-for-package%3Aflutter#creating-a-new-golden-file-test)
- [Updating a Golden File Test](https://github.com/flutter/flutter/wiki/Writing-a-golden-file-test-for-package%3Aflutter#updating-a-golden-file-test
)
- [First Time Contributors](https://github.com/flutter/flutter/wiki/Writing-a-golden-file-test-for-package%3Aflutter#first-time-contributors
)

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

When running `flutter test` locally without the `--update-goldens` flag, your test will pass, as it does not yet have a baseline for comparison on the [Flutter Gold](https://flutter-gold.skia.org/?query=source_type%3Dflutter) dashboard. The test will be recognized as new, and provide output for validation.

When you are happy with your image, you are ready to submit your PR for review. The reviewer should also verify your golden image(s), so make sure to include the golden(s) in your PR description. 

New test results will be compiled into a tryjob on the Flutter Gold dashboard, under [ChangeLists](https://flutter-gold.skia.org/changelists). There you will see your pull request and the associated golden files that were generated. 

New tests can be triaged from these tryjobs, which will cause the pending `flutter-gold` check to pass. Review the tryjob and the images that were generated, making sure they look as expected. Currently, we generate images for Linux, Mac, Windows, and Web platforms. It is common for there to be slight differences between them. Click the checkmark to approve the change, completing triage.

And that’s it! Your new golden file(s) will be checked in as the baseline(s) for your new test(s), and your PR will be ready to merge. :tada:

*Triage permission is currently restricted to googlers, with plans to extend to members of flutter-hackers. For more information, see [Contributor Access](https://github.com/flutter/flutter/wiki/Contributor-access).* 

## Updating a Golden File Test

If renderings change, then the golden baseline in [Flutter Gold](https://flutter-gold.skia.org/?query=source_type%3Dflutter) will need to be updated.

When developing your change, local testing will produce a failure along with visual diff output. This visual diff will be generated using the golden baseline from [Flutter Gold](https://flutter-gold.skia.org/?query=source_type%3Dflutter) that matches the current paramset of your testing environment (currently based on platform). This allows for quick iterations and validation of your change. Locally, this test will not pass until the new baseline has been checked in.

When you are happy with your golden change, you are ready to submit your PR for review. The reviewer should also verify your golden image(s), so make sure to include the golden changes you have made in your PR description. Changes to tests will be compiled into a tryjob on the Flutter Gold dashboard, under [ChangeLists](https://flutter-gold.skia.org/changelists). There you will see your pull request and the associated golden files that were generated, as well as the visual differences between the pre-existing baselines. 

The updated tests can be triaged from these tryjobs, which will cause the pending `flutter-gold` check to pass. Review the tryjob and the images that were generated, making sure they look as expected. Currently, we generate images for Linux, Mac, Windows and Web platforms. It is common for there to be slight differences between them. Click the checkmark to approve the change, completing triage.

Changes such as these constitute a **breaking change**, and should follow [Handling Breaking Changes](https://github.com/flutter/flutter/wiki/Tree-hygiene#handling-breaking-changes). This includes marking your PR with the `severe: API break` and `will affect goldens` labels, as well as updating the [Changelog](https://github.com/flutter/flutter/wiki/Changelog). Exceptions to this include new golden file tests and upstream changes, e.g. from a skia dependency.

And that’s it! Your new golden file(s) will be checked in as the baseline(s) for your new test(s), and your PR will be ready to merge. :tada:

*Triage permission is currently restricted to googlers, with plans to extend to members of flutter-hackers. For more information, see [Contributor Access](https://github.com/flutter/flutter/wiki/Contributor-access).* 

## First-Time Contributors

If you are a first-time contributor making a golden file change, first of all welcome!

By following the preceding guide to golden file testing, you may have found that pre-submit testing does not generate a tryjob on the [Flutter Gold](https://flutter-gold.skia.org/?query=source_type%3Dflutter) dashboard. In order for Gold to authenticate during pre-submit testing, contributor permissions are also authenticated. As a first-time contributor, the necessary permissions will not be granted until you land your first change.

You can still make changes to golden files though! In this special case, cc **@Piinks** on your pull request for assistance. An _ignore_ can be put in place on the [Flutter Gold](https://flutter-gold.skia.org/?query=source_type%3Dflutter) dashboard that is associated with the affected test(s) and pull request, making it possible to land your change. Currently, checking in golden files this way requires the assistance of a Googler, who will also need to triage the image after it lands.
