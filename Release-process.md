This is the process for updating the "alpha" branch.

1. Ensure everything is green [on Travis](https://travis-ci.org/flutter/flutter/builds) and [on the infra bots](https://build.chromium.org/p/client.flutter/waterfall) and on the cocoon dashboard (if you are not a Googler, please ask one to check the dashboard for you).
2. Ensure there are no outstanding [bugs in the "Today" milestone](https://github.com/flutter/flutter/issues?q=is%3Aopen+is%3Aissue+milestone%3AToday).
3. Notify the Flutter Hackers internal Hangout, and the [Gitter](https://gitter.im/flutter/flutter), that you updating the alpha branch. Wait for a few minutes to make sure people have a chance to let you know of any reason to not release the alpha branch.
4. Ensure your local workspace is configured according to the instructions in Flutter's [`CONTRIBUTING.md`](https://github.com/flutter/flutter/blob/master/CONTRIBUTING.md).
5. Run: `git fetch upstream`
6. Run: `git reset upstream/master --hard`
7. Run: `git push upstream HEAD:alpha` 
8. Email flutter-dev@googlegroups.com lists notifying them that alpha is updated. Please include a list of changes, if you're aware of them. If you are a Googler, also e-mail the Google-internal flutter-users@google.com list.
8. Done!