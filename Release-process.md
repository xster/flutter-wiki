This is the process for updating the "alpha" branch.

1. Ensure there are no issues labeled [TODAY](https://github.com/flutter/flutter/labels/%E2%9A%A0%20TODAY). These are issues people have found that are critical regressions.
1. On a clean branch of the Flutter repository, run:
   ```
   pushd dev/tools; pub get; popd
   bin/cache/dart-sdk/bin/dart dev/tools/update_versions.dart --release
   ```
   The script will output commands to tag the repo at a later step. Do not run these yet.
1. Submit a PR for the resulting diff. Await an LGTM and green tests and a green build, as for a normal patch.
1. Fetch the change locally:
   ```
   git fetch upstream
   git reset upstream/master --hard 
   ```
1. Wait for your patch to have cycled through all the bots and for everything to be green [on Travis](https://travis-ci.org/flutter/flutter/builds) and [on the infra bots](https://build.chromium.org/p/client.flutter/waterfall) and on the cocoon dashboard. The quick way to check if everything is green is to load https://flutter-dashboard.appspot.com/api/public/build-status and ensure is says `{"AnticipatedBuildStatus":"Succeeded"}`.
1. Notify everyone on [our Gitter channel](https://gitter.im/flutter/flutter) that you updating the alpha branch. Give 30-60 minutes notice to make sure people have a chance to let you know of any reason to not release the alpha branch.
1. Ensure your local workspace is configured according to the instructions in Flutter's [`CONTRIBUTING.md`](https://github.com/flutter/flutter/blob/master/CONTRIBUTING.md) and that you're current directory is a flutter/flutter clone.
1. Create a git tag with the SDK version. The `update_versions.dart` will have output the commands, otherwise substitute the version from the `VERSION` file into the following commands:
   ```shell
   git tag $VERSION
   git push upstream $VERSION
   git push upstream --tags
   ```
1. Roll alpha:
   ```
   git push upstream HEAD:alpha
   ```
1. On a clean branch of the Flutter repository, update the SDK and package versions to dev releases:
   ```
   bin/cache/dart-sdk/bin/dart dev/tools/update_versions.dart --increment
   ```
1. Submit a PR for the resulting diff. Await an LGTM and green tests and a green build, as for a normal patch.
1. Write a script to automate this process.
1. Done!