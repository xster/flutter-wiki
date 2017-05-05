This is the process for updating the "alpha" branch.

1. Ensure there are no issues labeled [TODAY](https://github.com/flutter/flutter/labels/%E2%9A%A0%20TODAY). These are issues people have found that are critical regressions.
2. Post a PR that modifies the version number of the main Flutter package's [`pubspec.yaml`](https://github.com/flutter/flutter/blob/master/packages/flutter/pubspec.yaml) by removing the `-dev` suffix.
3. Wait for your patch to have cycled through all the bots and for everything to be green [on Travis](https://travis-ci.org/flutter/flutter/builds) and [on the infra bots](https://build.chromium.org/p/client.flutter/waterfall) and on the cocoon dashboard. The quick way to check if everything is green is to load https://flutter-dashboard.appspot.com/api/public/build-status and ensure is says `{"AnticipatedBuildStatus":"Succeeded"}`.
4. Notify [Gitter](https://gitter.im/flutter/flutter), that you updating the alpha branch. Wait for a few minutes to make sure people have a chance to let you know of any reason to not release the alpha branch.
5. Ensure your local workspace is configured according to the instructions in Flutter's [`CONTRIBUTING.md`](https://github.com/flutter/flutter/blob/master/CONTRIBUTING.md) and that you're current directory is a flutter/flutter clone.
6. Run: `git fetch upstream`
7. Run: `git reset upstream/master --hard`
8. Run: `git push upstream HEAD:alpha` 
9. Post a PR that modifies the version number of the main Flutter package's [`pubspec.yaml`](https://github.com/flutter/flutter/blob/master/packages/flutter/pubspec.yaml) by incrementing the version number in some arbitrary way and adding a `-dev` suffix.
10. Write a script to automate this process.
11. Done!