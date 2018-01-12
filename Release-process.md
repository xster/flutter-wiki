## Rolling the dev channel

This is the process for updating the "alpha" branch. This should happen _at least_ once a week, ideally more often.

1. If today is a Friday, Saturday, or Sunday, go have fun until Monday. We don't roll over the weekend.
1. Check that the tree is green [on the cocoon dashboard](https://flutter-dashboard.appspot.com/build.html) (which includes the status of our builds [on Travis](https://travis-ci.org/flutter/flutter/builds) and [on the infra bots](https://build.chromium.org/p/client.flutter/waterfall)). If the tree isn't green, please work with the relevant engineers to fix the build, reverting bad patches if necessary.
1. Ensure there are no issues labeled [TODAY](https://github.com/flutter/flutter/labels/%E2%9A%A0%20TODAY). These are issues people have found that are critical regressions. If there are any such bugs, please work with the relevant engineers to have them fixed.
1. Verify that the Flutter codelabs, [Building Beautiful UIs with Flutter](https://codelabs.developers.google.com/codelabs/flutter/#0) and [Firebase for Flutter](https://codelabs.developers.google.com/codelabs/flutter-firebase/index.html#0), run normally and are still accurate. For simple updates to the friendlychat sample code, create PRs against [flutter/friendlychat-steps](https://github.com/flutter/friendlychat-steps) repo. For all codelab changes, create [issues](https://github.com/flutter/flutter/issues) with the [dev: docs - codelab](https://github.com/flutter/flutter/labels/dev%3A%20docs%20-%20codelab) and [TODAY](https://github.com/flutter/flutter/labels/%E2%9A%A0%20TODAY) labels.
1. Write tests for the codelabs so that the next person won't have to check them manually.

1. Notify everyone on [our Gitter channel](https://gitter.im/flutter/flutter) that you're starting to update the alpha branch.
1. On a clean branch of the Flutter repository, run:
   ```
   pushd dev/tools; pub get; popd
   bin/cache/dart-sdk/bin/dart dev/tools/update_versions.dart --release
   ```
   The script will output commands to tag the repo at a later step. Do not run these yet.
1. Commit locally, push to your fork, and submit a PR for the resulting diff. Await an LGTM, green tests and a green build, as for a normal patch, and then merge it.
1. Fetch the change locally:
   ```
   git fetch upstream
   git reset upstream/master --hard 
   ```
1. Wait for your patch to have cycled through all the bots and for everything to be green [on Travis](https://travis-ci.org/flutter/flutter/builds) and [on the infra bots](https://build.chromium.org/p/client.flutter/waterfall) and [on the cocoon dashboard](https://flutter-dashboard.appspot.com/build.html).
1. Check in again on [our Gitter channel](https://gitter.im/flutter/flutter) and let people know that you're still updating the alpha branch. Wait at least 30-60 minutes since your first announcement to make sure people have a chance to let you know of any reason to not release the alpha branch.
1. Ensure your local workspace is configured according to the instructions in Flutter's [`CONTRIBUTING.md`](https://github.com/flutter/flutter/blob/master/CONTRIBUTING.md) and that your current directory is a flutter/flutter clone.
1. Create a git tag with the SDK version. The `update_versions.dart` will have output the commands; otherwise substitute the version from the `VERSION` file into the following commands:
   ```shell
   git tag $VERSION
   git push upstream $VERSION
   ```
1. Roll alpha:
   ```
   git push upstream HEAD:alpha
   ```
1. On a clean branch of the Flutter repository, update the SDK and package versions to dev releases:
   ```
   bin/cache/dart-sdk/bin/dart dev/tools/update_versions.dart --increment
   ```
1. Commit locally, push to your fork, and submit a PR for the resulting diff. Await an LGTM and green tests and a green build, as for a normal patch.
1. Done!

If a bug is subsequently found on this release, please mark that commit as bad on the [[Bad Builds]] page.


## Rolling the beta channel

This is the process for rolling the "beta" branch. The beta branch holds a version of Flutter that we have verified (through at least a week of usage on the alpha branch) as having no new serious bugs. We intend to release to the beta branch on a predictable schedule, though that schedule hasn't yet been published.

1. If today is a Friday, Saturday, or Sunday, go have fun until Monday. We don't roll over the weekend.
1. Pick a [recent dev build](https://github.com/flutter/flutter/tags) that is at least seven days old, that is not listed on the [[Bad Builds]] page, and that is newer than the current latest commit on the `beta` branch.
1. Push that commit to the `beta` branch (vX.Y.Z is the tag of the selected version):
   ```
   git checkout vX.Y.Z
   git push upstream HEAD:beta
   ```
1. Send an e-mail to flutter-dev that includes the latest section of the [[Changelog]] page.
1. Update the [[Changelog]] page so that the current set of changes is now labeled as being changes between the last two beta versions, and the top section is a new blank "Changes since..." section.
1. Done!
