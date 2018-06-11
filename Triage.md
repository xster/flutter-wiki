# Guidelines for triage

The process of triaging bugs is to look at [the least recently updated bug with no labels](https://github.com/flutter/flutter/issues?q=is%3Aopen+is%3Aissue+no%3Alabel+sort%3Aupdated-asc), and to go through the following steps. Then, once the bug has been properly triaged per these steps, go back and look at whatever is now the least recently updated bug with no labels, and continue this process while there are bugs to process in this way.

## Overview

First, look at the bug report, and try to understand what the described problem is. Edit the original comment to remove boilerplate that the bug reporter didn't remove. Edit the original comment to add backticks (\`\`\`) around blocks of stack traces, code, the output of shell scripts like `flutter doctor`, etc. These changes make the bug much easier to manage.

If the bug is **still unclear** -- we have previously asked for more detail, and the bug reporter has had a chance to provide additional feedback, but has not been able to do so in a way that makes the bug actionable -- either apologise for us not being able to fix it and then close the bug, or add the `waiting for customer response` label, depending on your confidence that the reporter will be able to eventually provide sufficient detail. Then, skip the remaining steps.

If their report is **unclear**, doesn't give sufficient steps to reproduce, or is otherwise lacking in sufficient detail for us to act on it, add a polite comment asking for additional information, then skip the remaining steps.

If the issue describes something that you know for a fact has been **fixed** since the bug report was filed, add a cheerful comment saying so, close the issue, and skip the remaining steps.

If the bug is **clear enough** for us to act on it, continue with the following steps.

## Duplicates

If you recognise that this bug is a duplicate of an existing bug, add a reference to that bug in a comment, then close the bug. Skip the remaining steps. As you triage more and more bugs you will become more and more familiar with the existing bugs and so you will get better and better at marking duplicates in this way.

## Labels

**General rule: The more labels an issue has, the better!** _See also: [List of labels](https://github.com/flutter/flutter/labels)_

If any of the labels prefixed with "a:" apply to the issue, add those labels. If the bug is specific to a particular platform, consider adding one of the "platform-*" labels.

Add any of the applicable "severe: *" labels; typically only one will apply but sometimes "regression" will apply in conjunction with one of the others. If it's a very serious bug that should block releases, consider adding the "TODAY" label as well.

If it's something that looks trivial to fix, add the `easy bug` label.

Finally, label the issue based on what area of the project the bug relates to:

- If it's a documentation bug, add one of the labels with the "d:" prefix.
- If it's a bug with the underlying engine, add `engine` and optionally one of the bugs with the "e:" prefix. Consider one of the bugs with the "dependency" prefix if the bug is related to one of those areas.
- If it's a bug with the Flutter framework, add `framework` and optionally one of the bugs with the "f:" prefix.
- If it's a bug with plugins, add `plugin` and optionally one of the labels with the "p:" prefix.
- If it's a bug with the "flutter" tool, add `tool` and optionally one of the labels with the "t:" prefix.

## Milestone

_See also: [List of milestones](https://github.com/flutter/flutter/milestones?direction=asc&sort=due_date&state=open)_

Make a judgement call:

- If the bug fits our definition of "Flutter 1.0", add it to the "Goals" milestone.

- If the bug seems like a far fetched future request, add it to the "Future" milestone.

- Otherwise, add it to the "Stretch goals" milestone.

## People

Add one of the following people to the bug:

- If it's engine-related, @cbracken

- If it's Dart-related, @a-siva

- If it's related to the "flutter" tool or Fuchsia, @tvolkert

- If it's related to Material widgets, @HansMuller.

- Otherwise, add @Hixie

## Additional comments

If you have something to say regarding the bug, for example if you happen to notice what the problem is, or if you have some insight based on having seen many other bugs over time, feel free to add a comment to that effect. Your experience is valuable and may help both the reporter and the rest of the Flutter team.


# Critical issue triage

When doing the critical issue triage (as opposed to the new issue triage as above), we check each bug in the following lists:

* [Blockers](https://github.com/flutter/flutter/issues?q=is%3Aopen+is%3Aissue+label%3A%22severe%3A+customer+blocker%22+sort%3Aupdated-asc)
* [Critical](https://github.com/flutter/flutter/issues?q=is%3Aopen+is%3Aissue+label%3A%22severe%3A+customer+critical%22+sort%3Aupdated-asc)
* [TODAY](https://github.com/flutter/flutter/issues?q=is%3Aopen+is%3Aissue+label%3A%22%E2%9A%A0+TODAY%22)
* [Overdue milestone](https://github.com/flutter/flutter/issues?q=is%3Aopen+is%3Aissue+milestone%3AOverdue+sort%3Acreated-asc)
* Current bucket in the [milestones](https://github.com/flutter/flutter/milestones?direction=asc&sort=due_date&state=open)
* [Bugs pending feedback](https://github.com/flutter/flutter/issues?q=is%3Aopen+is%3Aissue+label%3A%22waiting+for+customer+response%22+sort%3Aupdated-desc) - close bugs with outstanding requests after 4 weeks, remove the label for bugs with answers
* [Most requested features](https://github.com/flutter/flutter/issues?q=is%3Aopen+is%3Aissue+sort%3Areactions-%2B1-desc)