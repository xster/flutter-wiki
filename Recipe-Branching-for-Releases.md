## Context

Production builds of both the Flutter Framework and the Engine repositories are created on LUCI. The recipes (actual build scripts) live at [flutter.googlesource.com/recipes](https://flutter.googlesource.com/recipes), while the builder config is maintained in https://github.com/flutter/infra. At this time, there is no version pinning between either of these repositories and the source trees they build (the Flutter framework & engine). This leads to frequent CI failures when preparing beta and stable releases because of changes in the recipes since the release was originally built on master.

When a dev release is promoted to beta, copies will be made of the engine and framework recipes (and additional dependencies) from the time the release was branched off of master will be made, namespaced by the name of the targeted stable version (e.g. `engine.py` -> `engine_v1_17_0.py`).

The LUCI builder config now has separate builders for stable, beta, and dev channels, with stable and beta having their own recipes. With each new beta release, builder config should be updated to reflect the new version to look up the recipe by and which refs to trigger builds by.

When promoting a beta release to stable, we can delete any recipe copies older than the new stable. We then update constants in the builder config relating to stable version.