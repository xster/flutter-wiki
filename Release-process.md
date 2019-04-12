For an overview of the various Flutter release channels, see [[Flutter build release channels]].

## Rolling the dev channel

Google engineers regularly roll the master branch into the dev branch and as part of this process label the
build that was rolled with a version number. The process for doing this includes testing Flutter against a wide
range of Google-internal tests, which is why it is not currently a process that people outside Google can do.

Part of the process uses the [`roll_dev.dart`](https://github.com/flutter/flutter/blob/master/dev/tools/lib/roll_dev.dart) script.

We are looking at what we can do to generalize this process so that we can run tests from other sources as
part of verifying the quality of a build we want to roll to `dev`, as part of which we would also return this
process to a public one which any contributor (including non-Google contributors) could do.

If you find a bug that should prevent us from rolling a particular `dev` build to our `beta` and `stable` channels, please mark that commit as bad on the [[Bad Builds]] page.


## Rolling the beta channel

This is the process for rolling the "beta" branch. The beta branch holds a version of Flutter that we have verified (through at least a week of usage on the dev branch) as having no new serious bugs.

We roll to the beta branch at the start of each month. Bugs intended to be fixed in a particular month's release are tagged with the milestone of that month, and need to be fixed by the last `dev` release of the previous month.

At the start of the month, start these steps. You will probably want to do this on macOS in order to test all parts of the codelabs and builds. These steps should be done using the [official download](https://flutter.io/get-started/install/) instead of a local checkout of the repo.

1. Pick a [recent dev build](https://github.com/flutter/flutter/tags) that:
    * was tagged in the previous month. You can see when a tag was added using `git log -1 --format=%ai v0.0.0`.
    * is not listed on the [[Bad Builds]] page.
    * is newer than the current [latest commit on the `beta` branch](https://github.com/flutter/flutter/commits/beta).
    * can be successfully upgraded _to_ from the dev build to which the beta branch currently points (via `git rebase v0.0.0 && flutter upgrade`)
    * can be successfully upgraded _from_ to a later dev build (via `git reset --hard v0.0.0 && git clean -f && flutter upgrade`)
    * can switch channels successfully (via 'flutter channel')
    * can be used to run the [[codelabs]]. You will have to manually run the build through all the current code labs to verify that the build is good. If someone has recently joined the team, they are a good candidate for running these tests, as it will help them learn Flutter at the same time!
    * can be used to build and run the Flutter Gallery:
        * via `flutter build apk`
        * via `flutter build ios`
        * via `flutter run` (on both Android and iOS)
1. Let other contributors know that you have picked a build and are planning on rolling the beta channel (in particular, let the contributor channel on Gitter know, let @Hixie know, and let @kf6gpe know).
1. Wait until seven days after your selected commit was rolled to `dev`.
1. Check that the build is still not listed on the [[Bad Builds]] page. If it has been marked as bad since you
started this process, then start over from step 1 but with the next oldest eligible build.
1. Push the selected commit to the `beta` branch (vX.Y.Z is the tag of the selected version):
   ```
   git fetch upstream
   git checkout vX.Y.Z
   git push upstream HEAD:beta
   ```
   If you get an error saying that you're not authorized to push to the branch, you need to be added to the list of "people and teams with push access" to the beta branch on GitHub. Contact a repository administrator (e.g. Hixie) to request that they add you to that list using [the beta branch configuration page](https://github.com/orgs/flutter/teams/beta-pushers/members). If the last pushed version was a hotfix, this may require temporarily unprotecting the branch.
1. Wait for the Cirrus builds on the beta branch to go green (make sure there's a green checkmark next to the branch at https://github.com/flutter/flutter/branches, and check https://ci.chromium.org/p/flutter/builders/prod/Mac%20Flutter%20Packaging, 
https://ci.chromium.org/p/flutter/builders/prod/Linux%20Flutter%20Packaging, and 
https://ci.chromium.org/p/flutter/builders/prod/Windows%20Flutter%20Packaging). If they fail, investigate the failure(s), and consider whether a newer dev build should be pushed to beta before any announcements are made.
1. Wait for the packaging build to complete, and download the [packages](https://flutter.io/sdk-archive/) built by them. On each system, do the following (these will be automated shortly, but until then...):
   - Unpack and check to see that a new project can be created with `flutter create --offline foo`
   - Check that it doesn't build a new snapshot the first time flutter runs.
   - Check that we have the right channel and version set.
1. Update the [[Changelog]] page so that the current set of changes is now labeled as being changes between the last two beta versions, and the top section is a new blank "Changes since..." section.
1. Inform the person currently doing the dev roll that they should increment the Y number instead of the Z number (`--increment=y`).
1. Move all the open bugs in the current milestone to the Overdue milestone.
1. Close the current [milestone](https://github.com/flutter/flutter/milestones?direction=asc&sort=due_date&state=open).
1. Send an e-mail to flutter-announce that includes the latest section of the [[Changelog]] page.
1. Done!

## Rolling the stable channel

This is the process for rolling the "stable" branch. We generally intend to roll the stable branch every quarter or so, probably with more fanfare than a regular beta roll. The steps below are the technical steps required for the roll; there will most likely be more coordination around the roll that are not discussed here.

1. Pick a commit that has been rolled to the `beta` branch using the process above. Typically, this will be done in coordination with a public event (e.g. Flutter Live) and so much discussion will have happened regarding exactly which commit to use, so this process doesn't go into detail as to how to select the commit.
1. Push the selected commit to the `stable` branch (vX.Y.Z is the tag of the selected version):
   ```
   git fetch upstream
   git checkout vX.Y.Z
   git push upstream HEAD:stable
   ```
   If you get an error saying that you're not authorized to push to the branch, you need to be added to the list of "people and teams with push access" to the stable branch on GitHub. Contact a repository administrator (e.g. Hixie) for advice. If the last pushed version was a hotfix, this may require temporarily unprotecting the branch.


## Applying emergency fixes

Sometimes there are security fixes that must be released as soon as possible. The process described below is intended to address this use case. (This process should not be used for non-security fixes. It is extremely risky to publish out-of-band releases like this and causes no end of trouble. Only the absolute most critical fixes are appropriate to be handled in this way.)

1. Let _TAG_ be the tag of the version of the framework that you are hot fixing, e.g. `v0.0.0`.
1. Let _VERSION_ be `$TAG-hotfix.1`, where `1` is the patch level (so if this is the second time that version is being hot fixed, first sorry, that sucks, and second, use `2`, and so forth). For example, `v0.0.0-hotfix.1`.
1. Let _BRANCH_ be `$TAG-hotfixes` (e.g. if _VERSION_ is `v0.0.0-hotfix.2` then _BRANCH_ is `v0.0.0-hotfixes`).
1. Let _CHANNEL_ be the channel you want to hotfix (e.g., `dev`, `beta`, `stable`).
1. If it doesn't yet exist, locally create _BRANCH_ on the framework repo starting from the framework commit of the build that you are fixing (`git fetch; git checkout $TAG -b $BRANCH`). Otherwise, switch to that branch (`git fetch; git checkout $BRANCH`).
1. Push this branch to GitHub. (`git push upstream $BRANCH`)
1. If this hot fix requires a change to the engine or its dependencies:
   1. Let _COMMIT_ be the engine commit of the build that you are fixing (as determined by `bin/internal/engine.version` on the Framework repo for _BRANCH_).
   1. Locally create a branch on the engine repo starting from that commit: `git checkout $COMMIT -b $VERSION`
   1. Update the branch accordingly, ideally by doing a `git cherry-pick` of the commit you need. Keep fixes to a strict minimum. If the fix involves applying a fix from an upstream dependency (e.g. Dart), use a hot fix release applied to the same original commit that the engine previously depended on; do not merely roll the dependency normally.
   1. Push this branch to your own GitHub fork of the engine (`git push --set-upstream origin $VERSION`).
   1. Browse to _COMMIT_ on GitHub (`https://github.com/flutter/engine/tree/$COMMIT`), then using the "Tree" dropdown on that GitHub page, create the branch named _VERSION_.
   1. Create a PR from your recently pushed branch, using the newly created branch as the base for the PR. As the PR description and commit message, enter information about why you're creating the branch.
   1. Once this is reviewed and the tests have run, land the PR _on the branch_. (Check that you're not landing it on master!)
   1. Force [LUCI](https://ci.chromium.org/p/flutter) (the Chromium continuous integration bots) to build the specific commit you just pushed. **This isn't currently possible. Talk to Hixie and Dan about how to trigger this.**
   1. Wait for the engine bots to have completed their work.
   1. Lock down the _VERSION_ branch on GitHub.
1. Update your local branch accordingly. If applicable, update the `engine.version` to point to the engine you just built.
1. Test this build on all platforms. Run the devicelab locally. Test the codelabs.
1. Push your local branch to your GitHub fork of the Flutter framework. (`git push origin $BRANCH`)
1. Create a PR from your recently pushed branch, using the _BRANCH_ branch as the base for the PR. As the PR description and commit message, enter information about why you're creating the hot fix.
1. Once the code is reviewed, land the PR onto the _BRANCH_ branch.
1. Tag your commit on _BRANCH_ as _VERSION_. (`git tag $TAG; git push upstream $VERSION`)
1. Force push the commit to the branch for the channel _CHANNEL_ you're hotfixing (`git push upstream HEAD:$CHANNEL`). If you get an error saying that you're not authorized to push to the branch, you need to be added to the list of "people and teams with push access" to the beta branch on GitHub. Contact a repository administrator (e.g. Hixie) to request that they add you to that list using [the beta branch configuration page](https://github.com/orgs/flutter/teams/beta-pushers/members). If the last pushed version was a hotfix, this may require temporarily unprotecting the branch as well.
1. If this is a beta release, wait for the Cirrus builds on the beta branch to go green (make sure there's a green checkmark next to the branch at https://github.com/flutter/flutter/branches, and check https://ci.chromium.org/p/flutter/builders/prod/Mac%20Flutter%20Packaging, 
https://ci.chromium.org/p/flutter/builders/prod/Linux%20Flutter%20Packaging, and 
https://ci.chromium.org/p/flutter/builders/prod/Windows%20Flutter%20Packaging). If they fail, let people know as soon as possible and investigate the failure(s) as a matter of urgency.
1. Wait for the packaging build to complete, and download the [packages](https://flutter.io/sdk-archive/) built by them. On each system, do the following (these will be automated shortly, but until then...):
   - Unpack and check to see that a new project can be created with `flutter create --offline foo`
   - Check that it doesn't build a new snapshot the first time flutter runs.
   - Check that we have the right channel and version set.
1. Mark _BRANCH_ as a protected branch on GitHub (you may need to ask a repo administrator, e.g. Hixie or kf6gpe, to do this).
  1. Under Branch rules add a branch rule for your branch.
  1. Enable "Require pull requests", "Require status checks to pass before merging", "Include administrators".
1. Send an e-mail to flutter-dev and flutter-announce regarding this update.

Note that after applying a hotfix to _CHANNEL_, you will need to manually force-push the subsequent release to that channel when it's ready. To do this:
1.  Ask a repo administrator (e.g., Hicksie, kf6gpe) to temporarily remove the branch protection on the branch for _CHANNEL_.
1.  Perform the force push using 
   ```
   git fetch upstream
   git checkout vX.Y.Z
   git push upstream HEAD:stable --force
   ```
1.  _Immediately_ ask a repo administrator to reinstate branch protection on the branch for _CHANNEL_