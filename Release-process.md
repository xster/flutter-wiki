This is the process for updating the "alpha" branch.

1. Ensure everything is green [on Travis](https://travis-ci.org/flutter/flutter/builds) and [on the infra bots](https://build.chromium.org/p/client.flutter/waterfall) and on the cocoon dashboard.  The quick way to check is to load https://flutter-dashboard.appspot.com/api/public/build-status and ensure is says "{"AnticipatedBuildStatus":"Succeeded"}".
2. Ensure there are no issues labeled [TODAY](https://github.com/flutter/flutter/labels/%E2%9A%A0%20TODAY).
3. Notify [Gitter](https://gitter.im/flutter/flutter), that you updating the alpha branch. Wait for a few minutes to make sure people have a chance to let you know of any reason to not release the alpha branch.
4. Ensure your local workspace is configured according to the instructions in Flutter's [`CONTRIBUTING.md`](https://github.com/flutter/flutter/blob/master/CONTRIBUTING.md) and that you're current directory is a flutter/flutter clone.
5. Run: `git fetch upstream`
6. Run: `git reset upstream/master --hard`
7. Run: `git push upstream HEAD:alpha` 
8. Done!