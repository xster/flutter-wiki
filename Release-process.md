For an overview of the various Flutter release channels, see [[Flutter build release channels]].

## Rolling the dev channel

This is the process for updating the "dev" branch. This should happen _at least_ once a week, ideally more often.

1. Check that the tree is green [on the cocoon dashboard](https://flutter-dashboard.appspot.com/build.html) (which includes the status of our builds [on Travis](https://travis-ci.org/flutter/flutter/builds) and [on the infra bots](https://build.chromium.org/p/client.flutter/waterfall)). If the tree isn't green, please work with the relevant engineers to fix the build, reverting bad patches if necessary.
1. Ensure there are no issues labeled [TODAY](https://github.com/flutter/flutter/labels/%E2%9A%A0%20TODAY). These are issues people have found that are critical regressions. If there are any such bugs, please work with the relevant engineers to have them fixed.
1. Ensure your local workspace is configured according to the instructions in Flutter's [`CONTRIBUTING.md`](https://github.com/flutter/flutter/blob/master/CONTRIBUTING.md) and that your current directory is a flutter/flutter clone.
1. On a clean branch of the Flutter repository, run:
   ```
   pushd dev/tools; pub get; popd
   bin/cache/dart-sdk/bin/dart dev/tools/lib/roll_dev.dart --increment=z
   ```
   ...and follow the prompts.

   If you are doing a dev roll that is the first roll since the last beta, then you should use `--increment=y` instead.
1. Done!

If a bug is subsequently found on this release, please mark that commit as bad on the [[Bad Builds]] page.


## Rolling the beta channel

This is the process for rolling the "beta" branch. The beta branch holds a version of Flutter that we have verified (through at least a week of usage on the dev branch) as having no new serious bugs. We intend to release to the beta branch on a predictable schedule, though that schedule hasn't yet been published.

A few days before the scheduled beta release date, start these steps (you will probably want to do this on macOS in order to test all parts of the codelabs and builds):

1. Pick a [recent dev build](https://github.com/flutter/flutter/tags) that:
    * is at least seven days old, meaning the tag was added at least 7 days ago (the commit itself will probably be even older!).
    * is not listed on the [[Bad Builds]] page.
    * is newer than the current [latest commit on the `beta` branch](https://github.com/flutter/flutter/commits/beta).
    * can be successfully upgraded _to_ from the dev build to which the beta branch currently points (via 'flutter upgrade')
    * can be successfully upgraded _from_ to a later dev build (via 'flutter upgrade')
    * can switch channels successfully (via 'flutter channel')
    * can be used to run the [[codelabs]]. You will have to manually run the build through all the current code labs to verify that the build is good.
    * can be used to build and run the Flutter Gallery:
        * via `flutter build apk`
        * via `flutter build ios`
        * via `flutter run` (on both Android and iOS)
1. Once you have found a good build, wait until the scheduled release date.
1. Push that commit to the `beta` branch (vX.Y.Z is the tag of the selected version):
   ```
   git fetch upstream
   git checkout vX.Y.Z
   git push upstream HEAD:beta
   ```
   If you get an error saying that you're not authorized to push to the branch, you need to be added to the list of "people and teams with push access" to the beta branch on GitHub. Contact a repository administrator to request that they add you to that list using [the beta branch configuration page](https://github.com/orgs/flutter/teams/beta-pushers/members).
1. Wait for the Cirrus builds on the beta branch to go green (make sure there's a green checkmark next to the branch at https://github.com/flutter/flutter/branches).  If they fail, investigate the failure(s), and consider whether a newer dev build should be pushed to beta before any announcements are made.
1. Wait for the packaging build to complete, and download the [packages](https://flutter.io/sdk-archive/) built by them. On each system, do the following (these will be automated shortly, but until then...):
   - Unpack and check to see that a new project can be created with `flutter create --offline foo`
   - Check that it doesn't build a new snapshot the first time flutter runs.
   - Check that we have the right channel and version set.
1. Update the [[Changelog]] page so that the current set of changes is now labeled as being changes between the last two beta versions, and the top section is a new blank "Changes since..." section.
1. Inform the person currently doing the dev roll that they should increment the Y number instead of the Z number (`--increment=y`).
1. Create [a new milestone](https://github.com/flutter/flutter/milestones/new) for the beta release after whatever the last beta release that has a milestone. For example, if there are currently milestones for beta12-beta18, create beta19, with a date one week before the scheduled date for that beta. (This should result in 7 open milestones with real dates including the current one which you're about to close below.)
1. Move all the open bugs in the current milestone to the Overdue milestone.
1. Close the current [milestone](https://github.com/flutter/flutter/milestones?direction=asc&sort=due_date&state=open).
1. Send an e-mail to flutter-dev that includes the latest section of the [[Changelog]] page.
1. Done!

## Applying emergency fixes

Sometimes there are security fixes that must be released as soon as possible. The process described below is intended to address this use case. (This process should not be used for non-security fixes. It is extremely risky to publish out-of-band releases like this and causes no end of trouble. Only the absolute most critical fixes are appropriate to be handled in this way.)

1. Let _TAG_ be the tag of the version of the framework that you are hot fixing, e.g. `v0.0.0`.
1. Let _VERSION_ be `$TAG-hotfix.1`, where `1` is the patch level (so if this is the second time that version is being hot fixed, first sorry, that sucks, and second, use `2`, and so forth). For example, `v0.0.0-hotfix.1`.
1. Let _OLDVERSION_ be _TAG_ if this is the first patch to this version, or else be the full branch name of the previous patch (e.g. if _VERSION_ is `v0.0.0-hotfix.2` then _OLDVERSION_ is `v0.0.0-hotfix.1`).
1. If this requires a change to the engine or its dependencies:
   1. Let _COMMIT_ be the engine commit of the build that you are fixing (as determined by `bin/internal/engine.version` on the Framework repo for _OLDVERSION_).
   1. Locally create a branch on the engine repo starting from that commit: `git checkout $COMMIT -b $VERSION`
   1. Update the branch accordingly, ideally by doing a `git cherry-pick` of the commit you need. Keep fixes to a strict minimum. If the fix involves applying a fix from an upstream dependency (e.g. Dart), use a hot fix release applied to the same original commit that the engine previously depended on; do not merely roll the dependency normally.
   1. Push this branch to your own GitHub fork of the engine (`git push origin $VERSION`).
   1. Browse to _COMMIT_ on GitHub (`https://github.com/flutter/engine/tree/$COMMIT`), then using the "Tree" dropdown on that GitHub page, create the branch named _VERSION_.
   1. Create a PR from your recently pushed branch, using the newly created branch as the base for the PR. As the PR description and commit message, enter information about why you're creating the branch.
   1. Once this is reviewed and the tests have run, land the PR _on the branch_. (Check that you're not landing it on master!)
   1. Force the chrome infra bots to build the specific commit you just pushed. (View the page for each bot, and force a build with the commit hash of the commit you just pushed.)
   1. Wait for the engine bots to have completed their work.
   1. Lock down the branch on GitHub.
1. Locally create a branch on the framework repo starting from the framework commit of the build that you are fixing. (`git checkout $OLDVERSION -b $VERSION`)
1. Push this branch to GitHub. (`git push upstream $VERSION`)
1. Update your local branch accordingly. If applicable, update the `engine.version` to point to the engine you just built.
1. Test this build on all platforms. Run the devicelab locally. Test the codelabs.
1. Push your local branch to your GitHub fork of the Flutter framework. (`git push origin $VERSION`)
1. Create a PR from your recently pushed branch, using the _VERSION_ branch as the base for the PR. As the PR description and commit message, enter information about why you're creating the hot fix.
1. Once the code is reviewed, land the PR onto the _VERSION_ branch.
1. If this is an update to the current `beta` build, also force push this commit to the `beta` branch.
1. Send an e-mail to flutter-dev regarding this update.