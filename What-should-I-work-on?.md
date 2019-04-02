This page attempts to be a one-stop shop for figuring out what the most important thing to work on is, so that team members (contributors) can determine the more effective way to improve Flutter.

1. Build breakage. Check the [dashboard](https://flutter-dashboard.appspot.com/build.html).
1. [TODAY bugs](https://github.com/flutter/flutter/issues?q=is%3Aopen+is%3Aissue+label%3A%22%E2%9A%A0+TODAY%22).
1. [Customer blockers](https://github.com/flutter/flutter/issues?q=is%3Aopen+is%3Aissue+label%3A%22severe%3A+customer+blocker%22).
1. Performance regressions. Check the [dashboard](https://flutter-dashboard.appspot.com/benchmarks.html).
1. [Filed regressions](https://github.com/flutter/flutter/issues?q=is%3Aopen+is%3Aissue+label%3A%22severe%3A+regression%22).
1. [Code review of open PRs](https://github.com/pulls?utf8=%E2%9C%93&q=is%3Aopen+is%3Apr+archived%3Afalse+user%3Aflutter+).
1. Reducing technical debt. (For example, increasing our test coverage by [writing new tests](https://github.com/flutter/flutter/wiki/Running-and-writing-tests).)
1. [Customer-critical bugs](https://github.com/flutter/flutter/issues?utf8=%E2%9C%93&q=is%3Aopen+is%3Aissue+label%3A%22severe%3A+customer+critical%22+).
1. [Assigned bugs](https://github.com/issues/assigned). We want to avoid slipping milestones, so once a bug is assigned it should also be given a named month milestone and we should ensure it's resolved by that date. See [[Issue Hygiene]].
1. The priorities described on our [[roadmap]].

Bugs in other bug systems should be tracked with bugs in GitHub.

Sometimes, items in the list above escalate. For example, a performance regression might get filed as a TODAY bug and thus start trumping a customer blocker issue.