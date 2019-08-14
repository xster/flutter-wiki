_(This page is referenced by comments in the Flutter codebase.)_

## Summary of rules for the golden repo

* All tests
  * Commit messages in [flutter/goldens](https://github.com/flutter/goldens) must reference the associated change in [flutter/flutter](https://github.com/flutter/flutter), in the form of `Goldens for https://github.com/flutter/flutter/issues/123456`

* Updated Tests
  * Don't update files, create new ones with an incremented version number.

  * Complete [Handling Breaking Changes](https://github.com/flutter/flutter/wiki/Tree-hygiene#handling-breaking-changes) when updating an existing golden file.

  * Delete obsolete files from [flutter/goldens](https://github.com/flutter/goldens) once your PR has landed.



## Adding a new golden file test

Write your test as a normal test, using `testWidgets` and `await tester.pumpWidget` and so on.

Put a `RepaintBoundary` widget around the part of the subtree that you want to verify. If you don't, the output will be a 2400x1800 image, since the tests by default use an 800x600 viewport with a device pixel ratio of 3.0.

Add an expectation along the following lines:

```dart
  await expectLater(
    find.byType(RepaintBoundary),
    matchesGoldenFile(
      'test_name.subtest.subfile.png',
      version: 0,
    ),
  );
```

The first argument is a finder that specifies the widget to screenshot.

The arguments to `matchesGoldenFile` are the filename for the screen shot and a version number. For the filename, the part up to the first dot should exactly match the test filename (e.g. if your test is `widgets/foo_bar_test.dart`, use `foo_bar`). The `subtest` part should be unique to this `testWidgets` entry, and the part after that should be unique within the `testWidgets` entry. This allows each file to have multiple `testWidgets` tests each with their own namespace for the images, and then allows for disambiguation within each test in case there are multiple screen shots per test. The version number is used to differentiate historical golden files and is appended to the end of the filename. When we update a golden file, a new file is created rather than updating the old one, and the version number provides distinction between the old and the new, see [Updating a Golden File](https://github.com/flutter/flutter/wiki/Writing-a-golden-file-test-for-package%3Aflutter/_edit#updating-a-golden-file) below for more information.

We chose Linux as the host platform upon which we run golden file tests. All of the reference images found in [flutter/goldens](https://github.com/flutter/goldens) are generated on Linux. If you don't have a Linux box and need someone to generate the goldens for you, cc **@Hixie** on your PR. Otherwise, the golden file test will be skipped on local platforms that are not Linux. This is due to slight rendering differences across platforms, as we don't yet have the infrastructure in place to allow the developer to update their golden files on all three supported host platforms. 

Once you have written your test, run `flutter test --update-goldens test/foo/bar_test.dart` in the `flutter` package directory (where the filename is the relative path to your new test). This will update the images in `bin/cache/pkg/goldens/packages/flutter/test/`; the directories below that will match the hierarchy of the directories in the `test` directory of the `flutter` package. Verify that the images are what you expect; update your test and repeat this step until you are happy.

Run `flutter test` again, but without the `--update-goldens` flag, to verify that the goldens match. If they don't, check that you're using the right filenames, and that they're all unique. It's not uncommon to copy-paste the expectation lines and so accidentally have duplicate golden filenames.

Commit the images to the goldens repo. You do this by going into the `bin/cache/pkg/goldens/` directory, which is actually a git checkout of our goldens repo, using `git add` for all your new files, using `git commit -a` to create a commit (in the commit message, link to the bug you are fixing, if possible, e.g. "Goldens for https://github.com/flutter/flutter/issues/17262"), and then pushing the files to the repo using `git push git@github.com:flutter/goldens.git master`.

This updates the goldens repo but does not make the images actually available yet. To make them available, you then update the `bin/internal/goldens.version` file in your Flutter repo to the hash of the [commit you just pushed to the goldens repo](https://github.com/flutter/goldens/commits/master).

Now run `flutter test` again, without the `--update-goldens` flag, to verify that the goldens match the uploaded images.

If they do, you are ready to submit your PR for review. The reviewer should also verify your golden files, so make sure to point to your goldens repo commit in your PR description. If you find the golden tests fail on some platforms, see the notes above about adding a skip line.


## Updating a golden file test

If renderings change, then rather than replacing the golden file in-situ, create new files with new names (incrementing the version number), and update the tests to point to those. Then, add the old file names [to the README file](https://github.com/flutter/goldens/edit/master/README.md).

This allows multiple people to contribute simultaneously without conflicting with each other.

Changes such as these constitute a breaking change, and should follow [Handling Breaking Changes](https://github.com/flutter/flutter/wiki/Tree-hygiene#handling-breaking-changes). This includes marking your PR with the `severe: API break` and `will affect goldens` labels, as well as updating the [Changelog](https://github.com/flutter/flutter/wiki/Changelog).

Once your main PR has landed, please come back and delete the obsolete files listed in the README.