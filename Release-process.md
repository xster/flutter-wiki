For an overview of the various Flutter release channels, see [[Flutter build release channels]].

## Rolling the dev channel

This is the process for updating the "dev" branch. This should happen _at least_ once a week, ideally more often.

1. If today is a Friday, Saturday, or Sunday, go have fun until Monday. We don't roll over the weekend.
1. Check that the tree is green [on the cocoon dashboard](https://flutter-dashboard.appspot.com/build.html) (which includes the status of our builds [on Travis](https://travis-ci.org/flutter/flutter/builds) and [on the infra bots](https://build.chromium.org/p/client.flutter/waterfall)). If the tree isn't green, please work with the relevant engineers to fix the build, reverting bad patches if necessary.
1. Ensure there are no issues labeled [TODAY](https://github.com/flutter/flutter/labels/%E2%9A%A0%20TODAY). These are issues people have found that are critical regressions. If there are any such bugs, please work with the relevant engineers to have them fixed.
1. Ensure your local workspace is configured according to the instructions in Flutter's [`CONTRIBUTING.md`](https://github.com/flutter/flutter/blob/master/CONTRIBUTING.md) and that your current directory is a flutter/flutter clone.
1. On a clean branch of the Flutter repository, run:
   ```
   pushd dev/tools; pub get; popd
   bin/cache/dart-sdk/bin/dart dev/tools/lib/roll_dev.dart --increment=z
   ```
   ...and follow the prompts.
1. Done!

If a bug is subsequently found on this release, please mark that commit as bad on the [[Bad Builds]] page.


## Rolling the beta channel

This is the process for rolling the "beta" branch. The beta branch holds a version of Flutter that we have verified (through at least a week of usage on the dev branch) as having no new serious bugs. We intend to release to the beta branch on a predictable schedule, though that schedule hasn't yet been published.

A few days before the scheduled beta release date, start these steps:

1. Pick a [recent dev build](https://github.com/flutter/flutter/tags) that:
    * is at least seven days old.
    * that is not listed on the [[Bad Builds]] page.
    * that is newer than the current latest commit on the `beta` branch.
    * that can be used to run the [[codelabs]]. You will have to manually run the build through all the current code labs to verify that the build is good.
1. Once you have found a good build, wait until the scheduled release date.
1. Push that commit to the `beta` branch (vX.Y.Z is the tag of the selected version):
   ```
   git checkout vX.Y.Z
   git push upstream HEAD:beta
   ```
1. Send an e-mail to flutter-dev that includes the latest section of the [[Changelog]] page.
1. Update the [[Changelog]] page so that the current set of changes is now labeled as being changes between the last two beta versions, and the top section is a new blank "Changes since..." section.
1. Done!
