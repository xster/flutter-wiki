1. Ensure everything is green [on Travis](https://travis-ci.org/flutter/flutter/builds) and [on the infra bots](https://build.chromium.org/p/client.flutter/waterfall).
2. Ensure there are no outstanding [bugs in the "Today" milestone](https://github.com/flutter/flutter/issues?q=is%3Aopen+is%3Aissue+milestone%3AToday).
3. Notify #flutter that you updating the alpha branch.
4. Ensure your local workspace is configured according to the instructions in Flutter's [`CONTRIBUTING.md`](https://github.com/flutter/flutter/blob/master/CONTRIBUTING.md).
5. Run: `git fetch upstream`
6. Run: `git reset upstream/master --hard`
7. Run: `git push upstream HEAD:alpha` 
8. Done!