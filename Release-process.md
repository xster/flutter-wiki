This is the process for updating the "alpha" branch.

0. Remove the code in the flutter tool that upgrades you from alpha to master (what does this mean? Can we file a bug to remove it?)
1. Ensure everything is green [on Travis](https://travis-ci.org/flutter/flutter/builds) and [on the infra bots](https://build.chromium.org/p/client.flutter/waterfall) and on the cocoon dashboard -- Don't we now have a web API to check this?
2. Ensure there are no issues labeled [TODAY](https://github.com/flutter/flutter/labels/%E2%9A%A0%20TODAY).
3. Notify [Gitter](https://gitter.im/flutter/flutter), that you updating the alpha branch. Wait for a few minutes to make sure people have a chance to let you know of any reason to not release the alpha branch.
4. Ensure your local workspace is configured according to the instructions in Flutter's [`CONTRIBUTING.md`](https://github.com/flutter/flutter/blob/master/CONTRIBUTING.md).
5. Run: `git fetch upstream`
6. Run: `git reset upstream/master --hard`
7. Run: `git push upstream HEAD:alpha` 
8. Done!