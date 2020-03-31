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
    * was tagged in the previous month. You can see when a given tag (eg `X.Y.Z`) was added using `git log -1 --format=%ai vX.Y.Z`.
    * is not listed on the [[Bad Builds]] page.
    * is newer than the current [latest commit on the `beta` branch](https://github.com/flutter/flutter/commits/beta).
    * can be successfully upgraded _to_ from the dev build to which the beta branch currently points. The upgrade path has no way to upgrade to a specific release; it always upgrades the user to the latest release on that channel. Thus, the closest approximation to this check you can perform is to simulate a user who is on the dev channel _at the current beta release_ and who runs `flutter upgrade`:
        1. Downloaded the latest beta channel release from https://flutter.dev/docs/development/tools/sdk/releases.  We'll refer to this release as `v<X.Y.Z>`.
        1. Unpack the downloaded zip file into a clean directory, and navigate into that directory
        1. Run `./bin/flutter doctor` to make sure the archive is working and reports the current beta version
        1. Run `git log` to double-check that the archive is at the right commit
        1. Run `./bin/flutter channel dev` to switch to the dev channel
        1. Run `git reset --hard v<X.Y.Z>` to fake being on that release while on the dev channel
        1. Run `./bin/flutter upgrade`
        1. Run `flutter doctor` and `git log` to make sure you were correctly upgraded. You should be on the latest dev release.
    * can be successfully upgraded _from_ to a later dev build:
        1. Find and download the dev-channel release of the build you intend to roll to beta in https://flutter.dev/docs/development/tools/sdk/releases. We'll refer to this release as `v<ROLL>`.
        1. Unpack the downloaded zip file into a clean directory, and navigate into that directory
        1. Run `./bin/flutter doctor` to make sure the archive is working and reports the correct dev version (`v<ROLL>`) and dev channel
        1. Run `git log` to double-check that the archive is at the right commit
        1. Run `./bin/flutter upgrade`
        1. Run `flutter doctor` and `git log` to make sure you were correctly upgraded. You should be on the latest dev release.
    * can switch channels successfully (via 'flutter channel')
    * can be used to run the Write Your First Flutter App [[codelabs]], parts 1 and 2. You will have to manually run the build through these code labs to verify that the build is good. If someone has recently joined the team, they are a good candidate for running these tests, as it will help them learn Flutter at the same time!
    * can be used to build and run the Flutter Gallery:
        * via `flutter build apk`
        * via `flutter build ios`
        * via `flutter run` (on both Android and iOS)
1. Let other contributors know that you have picked a build and are planning on rolling the beta channel. In particular, the #announcements channel on Discord know, let @Hixie (ianh) know, let @kf6gpe (ray) know, and let @tvolkert (todd) know.
1. Wait until seven days after your selected commit was rolled to `dev`.
1. Check that the build is still not listed on the [[Bad Builds]] page. If it has been marked as bad since you
started this process, then start over from step 1 but with the next oldest eligible build.
1. Push the selected commit to the `beta` branch (vX.Y.Z is the tag of the selected version):
   ```
   git fetch upstream
   git checkout vX.Y.Z
   git push upstream HEAD:beta
   ```
   If you get an error saying that you're not authorized to push to the branch, you need to be added to the list of "people and teams with push access" to the beta branch on GitHub. Contact a repository administrator (e.g. Hixie) to request that they add you to that list using [the beta branch configuration page](https://github.com/orgs/flutter/teams/beta-pushers/members). If the last pushed version was a hotfix, this may require temporarily unprotecting the branch and force pushing the branch.
1. Wait for the Cirrus builds on the beta branch to go green (make sure there's a green checkmark next to the branch at https://github.com/flutter/flutter/branches, and check https://ci.chromium.org/p/flutter/builders/prod/Mac%20Flutter%20Packaging, 
https://ci.chromium.org/p/flutter/builders/prod/Linux%20Flutter%20Packaging, and 
https://ci.chromium.org/p/flutter/builders/prod/Windows%20Flutter%20Packaging). If they fail, investigate the failure(s), and consider whether a newer dev build should be pushed to beta before any announcements are made.
1. Wait for the packaging build to complete, and download the [packages](https://flutter.io/sdk-archive/) built by them. On each system, do the following (these will be automated shortly, but until then...):
   - Unpack and check to see that a new project can be created with `flutter create --offline foo`
   - Check that it doesn't build a new snapshot the first time flutter runs.
   - Check that we have the right channel and version set.
1. Update the [[Changelog]] page to add a new section for the next beta release (i.e. if you just rolled v1.14.x, add a new section for 1.15.x). Move the "NEXT AUTHOR:" entry from the rolled beta section to the new section so that future changes will be flagged for the next release.
1. Inform the person currently doing the dev roll that they should increment the Y number instead of the Z number (`--increment=y`).
1. Send an e-mail to flutter-announce that includes the latest section of the [[Changelog]] page.
1. Done!

## Rolling the stable channel

This is the process for rolling the "stable" branch. We generally intend to roll the stable branch every quarter or so, probably with more fanfare than a regular beta roll. The steps below are the technical steps required for the roll; there will most likely be more coordination around the roll that are not discussed here.

1. Before you begin, reach out to a Googler to ensure that you've looped in a Google privacy expert to review changes to the release that may affect user privacy.
1. Pick a commit that has been rolled to the `beta` branch using the process above. Typically, this will be done in coordination with a public event (e.g. Flutter Live) and so much discussion will have happened regarding exactly which commit to use, so this process doesn't go into detail as to how to select the commit.
1. Check to see if there are any [RELEASE BLOCKERS](https://github.com/flutter/flutter/issues?utf8=%E2%9C%93&q=label%3A%22RELEASE+BLOCKER%22+). If there are, follow the "emergency" steps below to hot-fix the candidate beta release. _Remove the label once you are done._ The Open/Closed status of a bug relates to the master branch, not to the beta branch, and thus it will be common for this label to be applied to closed bugs.
1. Check the analytics for the selected commit on the beta branch to see if it has any spikes in crashes, lost users, etc. If you don't have access to the analytics contact kf6gpe or Hixie.
1. Push the selected commit to the `stable` branch (vX.Y.Z is the tag of the selected version):
   ```
   git fetch upstream
   git checkout vX.Y.Z
   git push upstream HEAD:stable
   ```
   If you get an error saying that you're not authorized to push to the branch, you need to be added to the list of "people and teams with push access" to the stable branch on GitHub. Contact a repository administrator (e.g. @Hixie) for advice. If the last pushed version was a hotfix, this may require temporarily unprotecting the branch.
1. Wait for the Cirrus builds on the stable branch to go green (make sure there's a green checkmark next to the branch at https://github.com/flutter/flutter/branches, and check https://ci.chromium.org/p/flutter/builders/prod/Mac%20Flutter%20Packaging, 
https://ci.chromium.org/p/flutter/builders/prod/Linux%20Flutter%20Packaging, and 
https://ci.chromium.org/p/flutter/builders/prod/Windows%20Flutter%20Packaging). If they fail, let people know as soon as possible and investigate the failure(s) as a matter of urgency.
1. Double-check that the release archives have updated to point to the new release (https://storage.googleapis.com/flutter_infra/releases/releases_linux.json, https://storage.googleapis.com/flutter_infra/releases/releases_windows.json, and https://storage.googleapis.com/flutter_infra/releases/releases_macos.json).  Those URLs are cached, so you may have to wait for the cache TTL to expire before the pages show the new release.
1. Double-check that the API docs have updated at [api.flutter.dev](https://api.flutter.dev/). The footer at the bottom of the page shows the version against which the docs were built. It should show the stable release you just pushed.


## Applying emergency fixes

Sometimes there are security fixes that must be released as soon as possible. The process described below is intended to address this use case. (This process should not be used for non-security fixes. It is extremely risky to publish out-of-band releases like this and causes no end of trouble. Only the absolute most critical fixes are appropriate to be handled in this way.)

NB: The previous hotfix version procedure used a `-` instead of a `+` between the version string and the hotfix number. This registers as a _lower_ version in semver, which caused issues with packages using fairly tight environment constraints.

1. Let _TAG_ be the tag of the version of the framework that you are hot fixing, e.g. `vX.Y.Z`.
1. Let _VERSION_ be `$TAG+hotfix.1`, where `1` is the patch level (so if this is the second time that version is being hot fixed, first sorry, that sucks, and second, use `2`, and so forth). For example, `vX.Y.Z+hotfix.1`.
1. Let _BRANCH_ be `$TAG-hotfixes` (e.g. if _VERSION_ is `vX.Y.Z+hotfix.2` then _BRANCH_ is `vX.Y.Z-hotfixes`).
1. Let _CHANNEL_ be the channel you want to hotfix (e.g., `dev`, `beta`, `stable`).
1. If is doesn't exist yet, create _BRANCH_ on the framework repo by browsing to _COMMIT_ on GitHub (https://github.com/flutter/flutter/tree/$COMMIT), then using the "Tree" dropdown on that GitHub page, create the branch named _BRANCH_.
1. Switch to _BRANCH_ in your local Git clone (`git fetch upstream; git checkout -b $BRANCH upstream/$BRANCH`).
1. If this hot fix requires a change to the engine or its dependencies:
   1. Let _COMMIT_ be the engine commit of the build that you are fixing (as determined by `bin/internal/engine.version` on the Framework repo for _BRANCH_).
   1. If is doesn't exist yet, create _BRANCH_ on the engine repo by browsing to _COMMIT_ on GitHub (https://github.com/flutter/engine/tree/$COMMIT), then using the "Tree" dropdown on that GitHub page, create the branch named _BRANCH_.
   1. Switch to _BRANCH_ in your local engine Git clone (`git fetch upstream; git checkout -b $BRANCH upstream/$BRANCH`).
   1. Update the branch accordingly, ideally by doing a `git cherry-pick` of the commit you need. Keep fixes to a strict minimum. If the fix involves applying a fix from an upstream dependency (e.g. Dart), use a hot fix release applied to the same original commit that the engine previously depended on; do not merely roll the dependency normally.
   1. Push this branch to your own GitHub fork of the engine (`git push --set-upstream origin $BRANCH`).
   1. Create a PR from your recently pushed branch, using _BRANCH_ as the base for the PR. As the PR description and commit message, enter information about why you're creating the branch.
   1. Once this is reviewed and the tests have run, land the PR _on the branch_. (Check that you're not landing it on master!)
   1. Force [LUCI](https://ci.chromium.org/p/flutter) (the Chromium continuous integration bots) to build the specific commit you just pushed. **Talk to @Hixie and @dnfield for any problem triggering the build.**
      1. Let _ENGINE_COMMIT_ be the commit you just landed on the branch.
      1. For each builder in the list of [hotfix-engine builders](https://ci.chromium.org/p/flutter/g/hotfix-engine/builders):
         1. Let _BUILDER_NAME_ be the name of the builder, fitting the pattern `buildbucket/luci.flutter.prod/{BUILDER_NAME}`
         1. Run the following command in a terminal:
            ```sh
            bb add \
                -commit "https://chromium.googlesource.com/external/github.com/flutter/engine/+/$ENGINE_COMMIT" \
                "flutter/prod/$BUILDER_NAME"
            ```
      1. Alternatively, you can run [`force_luci_build.sh`](https://github.com/flutter/engine/blob/master/tools/luci/force_luci_build.sh), which encapsulates this logic.
      1. Alternatively, if you prefer a web UI over the `bb` tool, then for each [builder](https://ci.chromium.org/p/flutter/g/engine/builders), open the [RPC explorer](https://cr-buildbucket.appspot.com/rpcexplorer/services/buildbucket.v2.Builds/ScheduleBuild?request=%7B%20%20%20%20%22builder%22:%20%7B%20%20%20%20%20%20%20%20%22project%22:%20%22flutter%22,%20%20%20%20%20%20%20%20%22bucket%22:%20%22prod%22,%20%20%20%20%20%20%20%20%22builder%22:%20%22Mac%20Host%20Engine%22%20%20%20%20%7D,%20%20%20%20%22gitilesCommit%22:%20%7B%20%20%20%20%20%20%20%20%22host%22:%20%22chromium.googlesource.com%22,%20%20%20%20%20%20%20%20%22project%22:%20%22external/github.com/flutter/engine%22,%20%20%20%20%20%20%20%20%22ref%22:%20%22refs/heads/v1.4.9-hotfix.1%22,%20%20%20%20%20%20%20%20%22id%22:%20%224737fc5cd89b8f0136e927b00f2e159444b95a73%22%20%20%20%20%7D,%20%20%20%20%22requestId%22:%20%22lh_haxadasdfsfasdf22222131%22%7D#) and make the following changes:
         - Update the value in `builder.builder` to the name of the builder.
         - Update the value in `gitilesCommit.ref` to `refs/heads/$BRANCH`.
         - Update the value in `gitilesCommit.id` to `$ENGINE_COMMIT`.
         - Update the value in `requestId` to be a unique random string.
         - Press Shift+Enter to schedule the build. You have to do this for every builder.
      1. Sign the newly built engine artifacts following go/flutter-manual-codesign. Contact @fujino if you have problems.
   1. Wait for the engine bots to have completed their work.
   1. Lock down _BRANCH_ on GitHub if it's not already locked down.
1. Update your local branch accordingly (such as by `git cherry-pick -e`). If applicable, update the `engine.version` to point to the engine you just built.
1. Test this build on all platforms. Run the [devicelab locally](https://github.com/flutter/flutter/blob/master/dev/devicelab/README.md#running-tests-locally). Test the codelabs.
1. Push your local branch to your GitHub fork of the Flutter framework. (`git push origin $BRANCH`)
1. Create a PR from your recently pushed branch, using the _BRANCH_ branch as the base for the PR. As the PR description and commit message, enter information about why you're creating the hot fix. (A bot may change the base branch to master. Edit the PR to change it back to _BRANCH_).
1. Once the code is reviewed, land the PR onto the _BRANCH_ branch via a "squash merge". Do not land the PR on master!
1. Update your local Git clone so that you pick up the squashed merge commit and not your local cherry-pick commit: `git checkout master && git branch -D $BRANCH && git fetch upstream && git checkout -b $BRANCH upstream/$BRANCH`.
1. Tag your commit on _BRANCH_ as _VERSION_. (`git tag $VERSION; git push upstream $VERSION`)
1. Force push the commit to the branch for the channel _CHANNEL_ you're hotfixing (`git push upstream HEAD:$CHANNEL`). If you get an error saying that you're not authorized to push to the branch, you need to be added to the list of "people and teams with push access" to the beta branch on GitHub. Contact a repository administrator (e.g. Hixie) to request that they add you to that list using [the beta branch configuration page](https://github.com/orgs/flutter/teams/beta-pushers/members). If the last pushed version was a hotfix, this may require temporarily unprotecting the branch as well.
1. Wait for the Cirrus builds on the beta branch to go green (make sure there's a green checkmark next to the branch at https://github.com/flutter/flutter/branches, and check https://ci.chromium.org/p/flutter/builders/prod/Mac%20Flutter%20Packaging, 
https://ci.chromium.org/p/flutter/builders/prod/Linux%20Flutter%20Packaging, and 
https://ci.chromium.org/p/flutter/builders/prod/Windows%20Flutter%20Packaging). If they fail, let people know as soon as possible and investigate the failure(s) as a matter of urgency.
1. Wait for the packaging build to complete, and download the [packages](https://flutter.io/sdk-archive/) built by them. On each system, do the following (these will be automated shortly, but until then...):
   - Unpack and check to see that a new project can be created with `flutter create --offline foo`
   - Check that it doesn't build a new snapshot the first time flutter runs.
   - Check that we have the right channel and version set.
1. Mark _BRANCH_ as a protected branch on GitHub (you may need to ask a repo administrator, e.g. Hixie or kf6gpe, to do this). Once the _BRANCH_ is protected, further pushes will also require elevated permissions. Contact a repo administrator for assistance with that.
1. Send an e-mail to flutter-dev and flutter-announce regarding this update.

Note that after applying a hotfix to _CHANNEL_, you will need to manually force-push the subsequent release to that channel when it's ready. To do this:
1.  Ask a repo administrator (e.g., Hixie, kf6gpe) to temporarily remove the branch protection on the branch for _CHANNEL_.
1.  Perform the force push using 
   ```
   git fetch upstream
   git checkout vX.Y.Z
   git push upstream HEAD:stable --force
   ```
1.  _Immediately_ ask a repo administrator to reinstate branch protection on the branch for _CHANNEL_