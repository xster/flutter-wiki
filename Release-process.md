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
    * is not listed on the [[Bad Builds]] page.
    * is newer than the current [latest commit on the `beta` branch](https://github.com/flutter/flutter/commits/beta).
    * can be successfully upgraded _to_ from the dev build to which the beta branch currently points (via 'flutter upgrade')
    * can be successfully upgraded _from_ to a later dev builds (via 'flutter upgrade')
    * can switch channels successfully (via 'flutter channel')
    * can be used to run the [[codelabs]]. You will have to manually run the build through all the current code labs to verify that the build is good.
    * can be used to build and run the Flutter Gallery:
        * via `flutter build apk`
        * via `flutter run`
1. Once you have found a good build, wait until the scheduled release date.
1. Push that commit to the `beta` branch (vX.Y.Z is the tag of the selected version):
   ```
   git fetch upstream
   git checkout vX.Y.Z
   git push upstream HEAD:beta
   ```
1. Send an e-mail to flutter-dev that includes the latest section of the [[Changelog]] page.
1. Update the [[Changelog]] page so that the current set of changes is now labeled as being changes between the last two beta versions, and the top section is a new blank "Changes since..." section.
1. Done!

## Where to find released downloadable archives

As part of rolling a release, an "all in one" archive of the release is created ([by this script](https://github.com/flutter/flutter/blob/master/dev/bots/prepare_package.dart)) that has:

1. A clone of the git repo with the HEAD set to the hash of the release
1. A populated pub cache
1. A populated bin/cache with flutter tool snapshot
1. Pre-build IntelliJ configuration
1. A copy of MinGit on Windows

A particular archive can be found by consulting [this JSON file](https://storage.googleapis.com/flutter_infra/releases/releases.json).  Once the desired release has been located, the download link for it is the combination of the `base_url` with the `mac_archive`, `linux_archive`, or `win_archive` tags on the release entry, depending upon which platform you are interested in. The hash for the currently released archive for the dev and beta channels are listed under the `current_dev` and `current_beta` tags, respectively.