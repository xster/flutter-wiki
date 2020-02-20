This page attempts to be a one-stop shop for figuring out what the most important thing to work on is, so that team members (contributors) can determine the more effective way to improve Flutter.

1. Build breakage. Check the [dashboard](https://flutter-dashboard.appspot.com/build.html).
1. Mentoring promising new contributors.
1. [TODAY bugs](https://github.com/flutter/flutter/issues?q=is%3Aopen+is%3Aissue+label%3A%22%E2%9A%A0+TODAY%22).
1. [Customer blockers](https://github.com/flutter/flutter/issues?q=is%3Aopen+is%3Aissue+label%3A%22severe%3A+customer+blocker%22).
1. [Fixing flaky tests](https://github.com/flutter/flutter/issues?q=is%3Aopen+is%3Aissue+label%3A%22team%3A+flakes%22+sort%3Acomments-desc).
1. Performance regressions. Check the [dashboard](https://flutter-dashboard.appspot.com/benchmarks.html). See also reported [performance regressions](https://github.com/flutter/flutter/issues?utf8=%E2%9C%93&q=is%3Aopen+label%3A%22severe%3A+performance%22+label%3A%22severe%3A+regression%22+).
1. [Filed regressions](https://github.com/flutter/flutter/issues?q=is%3Aopen+is%3Aissue+label%3A%22severe%3A+regression%22).
1. [Code review of open PRs](https://github.com/pulls?utf8=%E2%9C%93&q=is%3Aopen+is%3Apr+archived%3Afalse+user%3Aflutter+).
1. Reducing technical debt. (For example, increasing [our test coverage](https://github.com/flutter/flutter/wiki/Test-coverage-for-package%3Aflutter) by [writing new tests](https://github.com/flutter/flutter/wiki/Running-and-writing-tests).)
1. [Assigned bugs](https://github.com/issues/assigned). We want to avoid slipping milestones, so once a bug is assigned it should also be given a named month milestone and we should ensure it's resolved by that date. See [[Issue Hygiene]].
1. The priorities described on our [[roadmap]], which may include:
    * [Customer-critical bugs](https://github.com/flutter/flutter/issues?utf8=%E2%9C%93&q=is%3Aopen+is%3Aissue+label%3A%22severe%3A+customer+critical%22+).
    * Bugs marked as [annoyances](https://github.com/flutter/flutter/issues?q=is%3Aopen+is%3Aissue+label%3A%22a%3A+annoyance%22+sort%3Areactions-%2B1-desc).
    * Bugs labeled as issues of [quality](https://github.com/flutter/flutter/issues?utf8=%E2%9C%93&q=is%3Aopen+is%3Aissue+label%3A%22a%3A+quality%22+sort%3Areactions-%2B1-desc+).
    * Bugs with the [crash](https://github.com/flutter/flutter/issues?utf8=%E2%9C%93&q=is%3Aopen+is%3Aissue+label%3A%22severe%3A+crash%22+sort%3Areactions-%2B1-desc+) label.
1. [Issues sorted by thumbs-up](https://github.com/flutter/flutter/issues?q=is%3Aissue+is%3Aopen+sort%3Areactions-%2B1-desc).
1. Everything else.

Bugs in other bug systems should be tracked with bugs in GitHub. OKRs should be reflected in the items listed above. For example, OKRs should reflect what the roadmap covers, expected customer blockers, and so forth. Work that is unique to a particular quarter would be represented by a filed bug with a milestone and assignee.

Sometimes, items in the list above escalate. For example, a performance regression might get filed as a TODAY bug and thus start trumping a customer blocker issue.

See also:

 * [[Issue Hygiene]], in particular the section on prioritization.