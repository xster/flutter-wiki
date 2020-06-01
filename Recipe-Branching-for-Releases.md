## Context

Production builds of both the Flutter Framework and the Engine repositories are created on LUCI. The recipes (actual build scripts) live at [flutter.googlesource.com/recipes](https://flutter.googlesource.com/recipes), while the builder config is maintained in https://github.com/flutter/infra. At this time, there is no version pinning between either of these repositories and the source trees they build (the Flutter framework & engine). This leads to frequent CI failures when preparing beta and stable releases because of changes in the recipes since the release was originally built on master.

When a dev release is promoted to beta, copies will be made of the engine and framework recipes (and additional dependencies) from the time the release was branched off of master will be made, namespaced by the name of the targeted stable version (e.g. `engine.py` -> `engine_v1_17_0.py`).

The LUCI builder config now has separate builders for stable, beta, and dev channels, with stable and beta having their own recipes. With each new beta release, builder config should be updated to reflect the new version to look up the recipe by and which refs to trigger builds by.

When promoting a beta release to stable, we can delete any recipe copies older than the new stable. We then update constants in the builder config relating to stable version.

## Prerequisites

You must have read/write access to and local clones of the following repositories:

* [Flutter Framework](https://github.com/flutter/flutter)
* [Flutter Engine](https://github.com/flutter/engine)
* [Flutter Infra](https://github.com/flutter/infra)
* [Flutter Recipes](https://flutter.googlesource.com/recipes)
* You should have Chromium's depot_tools [installed](https://commondatastorage.googleapis.com/chrome-infra-docs/flat/depot_tools/docs/html/depot_tools_tutorial.html#_setting_up) (necessary for making LUCI updates)

## Beta Release Procedure

1. Find the Flutter framework **master commit** that your release has branched off of, and set it as `$FRAMEWORK_REVISION`.
2. Identify the name of the stable version that this is a release candidate for, normalize dots with underscores, set as `$VERSION`:
```
# for 1.18.0-12.0.pre
VERSION='1_18_0'
```
3. Get the date/time this commit landed in the tree (since we use GitHub squash and merge, this will presumably be the commit date). The following command will retrieve the commit date of a given revision:
```
cd $FRAMEWORK_REPO
FRAMEWORK_DATE=$(git show -s --format=%ci $FRAMEWORK_REVISION)
```
4. Get the last LUCI recipe commit before the framework date:
```
cd $RECIPES_REPO
RECIPE_FRAMEWORK_REVISION=$(git log --before="$FRAMEWORK_DATE" -n 1 --format=%H)
```
5. Copy the framework recipe at the time of `$FRAMEWORK_DATE` as `flutter_beta.py` (for the engine, `engine_$VERSION.py`):
```
cd $RECIPES_REPO/recipes
git show $RECIPE_FRAMEWORK_REVISION:./flutter.py > "./flutter_$VERSION.py"
```
6. Edit `flutter_beta.py` with comments describing the release version this recipe is for, `$FRAMEWORK_REVISION`, `$FRAMEWORK_DATE`, and `$LUCI_FRAMEWORK_REVISION`.
7. Set `RELEASE_FRAMEWORK_REF` (e.g. "refs/heads/flutter-1.17-candidate.3") for what you want to test and trigger a test run of the recipe fork with LED:
```
led get-builder 'luci.flutter.prod:Linux' | led edit -pa git_ref="$RELEASE_FRAMEWORK_REF" | led edit -pa git_url='https://github.com/flutter/flutter' | led edit -pa recipe_name='flutter_v1_17_0.py' | led edit-recipe-bundle | led launch
```
8. If the LED run fails, possible reasons include:
  - Something changed in the builder config, see flutter/infra repo. (e.g. Xcode version in builder changed, requiring a recipe cherry pick from upstream version)
  - The recipe depends on other files in the repo, which haven't been forked. Repeat steps 4-6 for these additional dependencies.
9. Get the engine repo revision from your framework revision. In the framework repo, the engine revision is checked in as [//bin/internal/engine.version](https://github.com/flutter/flutter/blob/master/bin/internal/engine.version).
10. Repeat steps 2 to 7 for the engine recipe. As of release 1.17.0, `engine_builder.py` and `engine_builder.proto` also had to be forked. The LED command for testing engine should be:
```
led get-builder 'luci.flutter.prod:Linux Host Engine' | led edit -pa git_ref="$RELEASE_ENGINE_REF" | led edit -pa git_url='https://github.com/flutter/engine' | led edit -pa recipe_name=engine_v1_17_0.py | led edit-recipe-bundle | led launch
```
11. Update tests:
```
cd $RECIPES_REPO
./recipes.py test train
```
12. Commit the two new recipes and all updated test expectations to git. Create a new CL with `git cl upload` and get a reviewer from `build/scripts/slave/recipes/flutter/OWNERS`. Upon approval, merge the CL.
13. In flutter/infra, update relevant top level constants in `main.star`:
   - `STABLE_REFS`, a regex to the branch name of the current stable
   - `STABLE_VERSION`, the version element of the recipe filename, e.g. `v1_17_0`
   - `BETA_REFS`, a regex to the branch name of the current beta candidate
   - `BETA_VERSION`
   - `DEV_REFS`, a regex to the branch names of dev releases after incrementing y
14. Search the file for any TODOs related to the release you are doing.
15. Execute the main.star file to generate the rest of the config files (and validate your changes for mistakes): `$ ./main.star`
16. Commit your changes, push to github and get it reviewed. This PR should be landed after any LUCI recipe changes.
17. After your PR has landed, wait for it to be mirrored to [the chromium tree](https://chromium.googlesource.com/external/github.com/flutter/infra/). LUCI post-submit builds should now work for your candidate branch.

## Stable Release Procedure

Updating recipes for a stable release is much simpler than that for a beta release, as the requisite recipe should have already been forked when the release was promoted to beta.

1. In flutter/infra, update relevant top level constants in `main.star`:
   - `STABLE_REFS`, a regex to the branch name of the current stable
   - `STABLE_VERSION`, the version element of the recipe filename, e.g. `v1_17_0`
2. Execute the main.star file to generate the rest of the config files (and validate your changes for mistakes): `$ ./main.star`
3. Commit your changes, push to github and get it reviewed. Merge it. Note, this updated configuration won't take effect until it has propagated to LUCI infra. The current version of the config can be seen [here](https://luci-config.appspot.com/#/projects/flutter).
4. In the recipes repo, any recipe forks older than the current stable can be safely deleted. `git rm /path/to/recipe` will both delete the file and stage the change with git.
5. Update tests:
```
cd $RECIPES_REPO
./recipes.py test train
```
6. Commit the file deletions and updated test expectations to git. Create a new CL with `git cl upload` and get a reviewer from `build/scripts/slave/recipes/flutter/OWNERS`. Upon approval, merge the CL.