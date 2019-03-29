Several of our dependencies are automatically rolled (updated) by bots.

## Skia to Engine

We use an auto-roller for Skia rolls. It's status can be viewed at <https://skia-flutter-roll.skia.org/>. In case of  build failures or other errors, ping the Flutter-Skia chat channel. In case you get no response, you can login with an @google.com account and pause the roller (or ask someone with an @google.com account to do so). Please specify a descriptive reason and file a bug to re-enable the rollers as soon as possible.

The bot updates the `skia_revision` line of <https://github.com/flutter/engine/blob/master/DEPS>.

Skia also uses an auto-roller for Fuchsia; see <https://autoroll-internal.skia.org/r/fuchsia-autoroll>.

## Engine to Framework

The engine is automatically rolled to the framework.

The bot updates <https://github.com/flutter/flutter/blob/master/bin/internal/engine.version> to point to the latest revision of the engine *whose artifacts built successfully*, as determined by looking at the [Engine Console](https://ci.chromium.org/p/flutter/g/engine/console).

If you make a breaking change to the engine, you'll need to land the change to `engine.version` manually in
the same PR to the framework as the one where you fix the framework to work with the new API. In general, it
is very advisable to not make a breaking change to our APIs, and thus avoid this problem entirely.

When you change the `engine.version` file locally, you should delete `$FLUTTER_ROOT/bin/cache` and then run `flutter precache` to ensure that all your local artifacts and snapshots are updated. You can then run tests and be sure that they are running against the latest version of the assets you need.

You may find it helpful to use the [`$ENGINE_ROOT/src/flutter/tools/engine_roll_pr_desc.sh`](https://github.com/flutter/engine/blob/master/tools/engine_roll_pr_desc.sh) to create a PR description. Doing this helps us track down what commits have rolled in more quickly, and properly link to other commits and pull requests for commenting and tracking.

For example, to generate a description from hash deadbeef to beefdead:

```bash
$ ./tools/engine_roll_pr_desc.sh deadbeef..beefdead
```