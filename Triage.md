# Guidelines for triage

The process of triaging bugs is to first go through these bug lists and make sure they have all been processed as described below:

<!--ALL-LABELS-->
1. [the PRs with none of the classification labels](https://github.com/flutter/flutter/pulls?utf8=%E2%9C%93&q=is%3Apr+is%3Aopen+-label%3Aframework+-label%3A%22f%3A+cupertino%22+-label%3A%22f%3A+material+design%22+-label%3A%22a%3A+desktop%22+-label%3Atool+-label%3Ateam+-label%3A"☸+platform-web"+sort%3Aupdated-asc+).

<!--ALL-LABELS-->
2. [the bugs with none of the classification labels](https://github.com/flutter/flutter/issues?utf8=%E2%9C%93&q=is%3Aopen+is%3Aissue+-label%3Aframework+-label%3Aengine+-label%3Atool+-label%3Aplugin+-label%3Apackage+-label%3A%22d%3A+examples%22+-label%3A%22will+need+additional+triage%22+-label%3Ateam+-label%3A"☸+platform-web"+-label%3A%22a%3A+desktop%22+sort%3Aupdated-asc+-label%3A%22waiting+for+customer+response%22+).

3. [the bugs with no milestone](https://github.com/flutter/flutter/issues?q=is%3Aopen+is%3Aissue+no%3Amilestone).

Finally, one should look at [the least recently updated bugs of any kind](https://github.com/flutter/flutter/issues?q=is%3Aissue+is%3Aopen+sort%3Aupdated-asc) and see if they are still relevant, applying new labels, updating the subject line if necessary, providing an update if necessary, etc.

## Triaging PRs

When triaging PRs, it's important that incoming PRs:

* Contain tests for the functionality they're providing. If it's a bug fix, there should be tests to ensure that we don't regress the bug fix. If it's new functionality, the functionality should have tests as well.

* Code and tests should match our [Style guide](https://github.com/flutter/flutter/wiki/Style-guide-for-Flutter-repo). Look carefully for spelling mistakes and violations of our coding style; invite the contributor to fix these.

For PRs, each PR should match one of the following categories:

* It can be in a specific repository (e.g. the website or engine repository) that has a narrow scope. These do not need labels. (Only the "flutter" repository is considered to not have a narrow scope currently.)

<!--ALL-LABELS-->
* It can have one of the following labels: `framework`, `engine`, `f: material design`, `f: cupertino`, `tool`, `☸ platform-web`, `a: desktop`, `a: existing-apps`.

* It can have the `will need additional triage` label. 

Once a PR meets these basic requirements and has an appropriate repository and label, feel free to add the `will need additional triage` label to call attention to it from the relevant team.

## Triaging issues

First, look at the bug report, and try to understand what the described problem is. Edit the original comment to remove boilerplate that the bug reporter didn't remove. Edit the original comment to add backticks (\`\`\`) around blocks of stack traces, code, the output of shell scripts like `flutter doctor`, etc. These changes make the bug much easier to manage.

If their report is **unclear**, doesn't give sufficient steps to reproduce, or is otherwise lacking in sufficient detail for us to act on it, add a polite comment asking for additional information, then skip the remaining steps.

If the bug is **still unclear** -- we have previously asked for more detail, and the bug reporter has had a chance to provide additional feedback, but has not been able to do so in a way that makes the bug actionable -- either apologize for us not being able to fix it and then close the bug, or add the `waiting for customer response` label, depending on your confidence that the reporter will be able to eventually provide sufficient detail. Then, skip the remaining steps.

If the issue describes something that you know for a fact has been **fixed** since the bug report was filed, add a cheerful comment saying so, close the issue, and skip the remaining steps.

If the bug is **clear enough** for us to act on it, continue with the following steps.

### Duplicates

If you recognize that this bug is a duplicate of an existing bug, add a reference to that bug in a comment, then close the bug. Skip the remaining steps. As you triage more and more bugs you will become more and more familiar with the existing bugs and so you will get better and better at marking duplicates in this way.

### Requests for help (documentation issues)

If the bug report is a question, then it probably belongs in Stack Overflow or on our #help channel or some other forum for getting help. However, if it appears that the reporter did try to read our documentation to find the answer, and failed, or, if you look in our documentation and find it is inadequate here, then please consider it a documentation bug (and update the summary accordingly). 

If you are confident our official documentation (on flutter.dev or api.flutter.dev) fully answers their question, then provide a link to the relevant page and close the issue, being very polite and asking them to reopen if the documentation is not sufficiently clear for them.

### Labels

**General rule: The more labels an issue has, the better!** _See also: [List of labels](https://github.com/flutter/flutter/labels)_

If any of the labels prefixed with "a:" apply to the issue, add those labels. If the bug is specific to a particular platform, consider adding one of the "platform-*" labels. If it's specific to running on a desktop platform, include "a: desktop" as well.

Add any of the applicable "severe: *" labels; typically only one will apply but sometimes `severe: regression` will apply in conjunction with one of the others.

If it's a very serious bug that should block releases, consider adding the `TODAY` label as well. This label is reserved for build breaks, regressions in metrics, issues causing data loss for end users, etc. that would effectively block us from shipping tip of tree to users.

If it seems to affect a lot of people but doesn't quite rise to the level of a release blocker, add the `customer: crowd` label (which means it affects many people) and then either the `severe: customer blocker` label (if it's blocking people) or the `severe: customer critical` label (if it's not blocking people but is nonetheless serious). This will flag the bug for consideration during weekly triage (see the second section of this document).

If it's something that looks trivial to fix, add the `easy fix` label.

Finally, label the issue based on what area of the project the bug relates to:

<!--ALL-LABELS-->
- If it's a documentation bug, add one of the labels with the "d:" prefix.
- If it's a bug with the underlying engine, add `engine` and optionally one of the bugs with the "e:" prefix. Consider one of the bugs with the "dependency" prefix if the bug is related to one of those areas.
- If it's a bug with the Flutter framework, add `framework` and optionally one of the bugs with the "f:" prefix.
   - If it's specific to Material widgets or the Gallery, include the `f: material` label.
   - If it's specific to iOS widgets, include the `f: cupertino` label.
- If it's a bug with plugins, add `plugin` and optionally one of the labels with the "p:" prefix.
- If it's a bug with packages, add `package` and optionally one of the labels with the "p:" prefix.
- If it's a bug with the "flutter" tool, add `tool` and optionally one of the labels with the "t:" prefix.
- If it's specific to writing desktop Windows, macOS, Linux, or Web apps with Flutter, add the `a: desktop` label.
- If it's related to a specific platform, add one of the "platform" labels. In particular, issues involving the Web backend should have the `☸ platform-web` label.
- If it's an issue involving integrating Flutter as a library into an existing mobile application, add `a: existing-apps`.
- If it's an issue that is repeatedly frustrating developers when using non-experimental Flutter features (e.g. performing mobile app builds, dealing with text input, using supported IDEs), add `a: annoyance`. If unsure, consult with DevRel (filiph@). 

Bugs relating to the website should be moved to the `flutter/website` repo.

Bugs relating to the IDEs should be moved to the `flutter/flutter-intellij` repo.

### Milestone

_See also: [List of milestones](https://github.com/flutter/flutter/milestones?direction=asc&sort=due_date&state=open)_

Make a judgement call:

- If the bug seems like a correctness issue, relates to polish and quality, or might block fully shipping broad consumer-facing apps, add it to the "Goals" milestone.

- If the bug seems like a far fetched future request, add it to the "Future" milestone.

- Otherwise, add it to the "Stretch goals" milestone.


### Additional comments

If you have something to say regarding the bug, for example if you happen to notice what the problem is, or if you have some insight based on having seen many other bugs over time, feel free to add a comment to that effect. Your experience is valuable and may help both the reporter and the rest of the Flutter team.


# Critical triage

## Issues

When doing the critical issue triage (as opposed to the new issue triage as above), we check each bug in the following lists:

* [TODAY](https://github.com/flutter/flutter/issues?q=is%3Aopen+is%3Aissue+label%3A%22%E2%9A%A0+TODAY%22): all bugs should be assigned and progress should be being made on all of them.
* [Blockers](https://github.com/flutter/flutter/issues?q=is%3Aopen+is%3Aissue+label%3A%22severe%3A+customer+blocker%22+sort%3Aupdated-asc): all bugs should have an appropriate milestone (typically the current milestone, or for issues that we cannot do immediately, "Scheduled customer work"); bugs we are working on should be assigned; if there hasn't been an update in the last two weeks, an update should be posted.
* [Critical](https://github.com/flutter/flutter/issues?q=is%3Aopen+is%3Aissue+label%3A%22severe%3A+customer+critical%22+sort%3Aupdated-asc): all bugs should have an appropriate milestone.
* [Bugs](https://github.com/flutter/flutter/issues?q=is%3Aopen+label%3A%22will+need+additional+triage%22+sort%3Aupdated-asc) and [PRs](https://github.com/flutter/flutter/pulls?utf8=%E2%9C%93&q=is%3Aopen+label%3A%22will+need+additional+triage%22+sort%3Aupdated-asc+) flagged for additional triage: figure out what should be done with the bug, then remove the `will need additional triage` label.
* [Overdue milestone](https://github.com/flutter/flutter/issues?q=is%3Aopen+is%3Aissue+milestone%3AOverdue+sort%3Acreated-asc): all bugs should have their milestone and assignee updated. If the issue isn't being worked on, it should be unassigned and moved to a long-term milestone; do not merely keep pushing every issue to the next milestone.
* Upcoming [milestones](https://github.com/flutter/flutter/milestones?direction=asc&sort=due_date&state=open): all bugs should be assigned to engineers who have acknowledged that this is what they'll be working on.
* [Most requested features](https://github.com/flutter/flutter/issues?q=is%3Aopen+is%3Aissue+sort%3Areactions-%2B1-desc): none of the top 20ish bugs are new, and they've all been considered before and have appropriate milestones.
* [Most requested bugs](https://github.com/flutter/flutter/issues?utf8=%E2%9C%93&q=is%3Aopen+is%3Aissue+sort%3Areactions-%2B1-desc+-label%3A%22severe%3A+new+feature%22+): nothing surprising in the list; consider adding `customer: crowd` `customer critical` labels.

In addition, a bot takes care of these:
* [Bugs pending feedback](https://github.com/flutter/flutter/issues?q=is%3Aopen+is%3Aissue+label%3A%22waiting+for+customer+response%22+sort%3Aupdated-desc): close bugs with outstanding requests after 4 weeks, remove the label for bugs with answers.

## News

We also want to regularly check https://developer.apple.com/news/ for updates that might affect us.

## PRs

We regularly check that the following PRs are not being neglected, and have appropriate labels:

* [The flutter/flutter PRs with none of the classification labels](https://github.com/flutter/flutter/pulls?utf8=%E2%9C%93&q=is%3Apr+is%3Aopen+-label%3Aframework+-label%3A%22f%3A+cupertino%22+-label%3A%22f%3A+material+design%22+-label%3Atool+sort%3Aupdated-asc)

* [PRs in our esoteric repos](https://github.com/pulls?utf8=%E2%9C%93&q=is%3Aopen+is%3Apr+archived%3Afalse+user%3Aflutter+sort%3Aupdated-asc+-repo%3Aflutter%2Fflutter+-repo%3Aflutter%2Fengine+-repo%3Aflutter%2Fplugin-repo%3Aflutter%2Fpackage+-repo%3Aflutter%2Fwebsite+-repo%3Aflutter%2Fflutter-intellij)

* [All open PRs](https://github.com/pulls?q=is%3Aopen+is%3Apr+archived%3Afalse+user%3Aflutter+sort%3Aupdated-asc)


# Area-focused triage

We intend for each area of the product to go through the following triage regularly:

* Look at open PRs and review them.
* Look at open bugs and determine what needs to be worked on, looking at TODAY bugs, customer blockers, customer critical bugs, regressions, crashers, popular bugs and feature requests (thumbs-up reactions), and old issues.

Teams can use a variety of tools to prioritize bugs.

* One tool is the milestone field, in particular the "Goals", "Near-term goals", "Stretch goals", "Future", and "Scheduled customer work" milestones for work that doesn't yet have a committed target date and assigned engineer, and the dated milestones for bugs that have an assigned engineer and will definitely not slip beyond a particular month (be conservative when picking milestones!).
* Another tool is the [Projects](https://github.com/flutter/flutter/projects) page on GitHub. Feel free to create new projects as desired, and use it as you see fit.
* In some circumstances, we can create new milestones that parallel the dated milestones. Contact @Hixie before going down this route.

## Engine

* [Engine PRs](https://github.com/flutter/engine/pulls?q=is%3Apr+is%3Aopen+sort%3Aupdated-asc)
* [Buildroot PRs](https://github.com/flutter/buildroot/pulls?q=is%3Apr+is%3Aopen+sort%3Aupdated-asc)
* [TODAY bugs](https://github.com/flutter/flutter/issues?q=is%3Aopen+is%3Aissue+label%3Aengine+sort%3Aupdated-asc+label%3A%22TODAY%22)
* [Customer blockers](https://github.com/flutter/flutter/issues?q=is%3Aopen+is%3Aissue+label%3Aengine+sort%3Aupdated-asc+label%3A%22severe%3A+customer+blocker%22)
* [Customer critical issues](https://github.com/flutter/flutter/issues?q=is%3Aopen+is%3Aissue+label%3Aengine+sort%3Aupdated-asc+label%3A%22severe%3A+customer+critical%22)
* [Regressions](https://github.com/flutter/flutter/issues?q=is%3Aopen+is%3Aissue+label%3Aengine+sort%3Aupdated-asc+label%3A%22severe%3A+regression%22)
* [Crash bugs](https://github.com/flutter/flutter/issues?q=is%3Aopen+is%3Aissue+label%3Aengine+sort%3Aupdated-asc+label%3A%22severe%3A+crash%22)
* [Popular issues](https://github.com/flutter/flutter/issues?q=is%3Aopen+is%3Aissue+label%3Aengine+sort%3Areactions-%2B1-desc+-label%3A%22severe%3A+new+feature%22)
* [Popular feature requests](https://github.com/flutter/flutter/issues?q=is%3Aopen+is%3Aissue+label%3Aengine+sort%3Areactions-%2B1-desc+label%3A%22severe%3A+new+feature%22)
* [Old issues](https://github.com/flutter/flutter/issues?q=is%3Aopen+is%3Aissue+label%3Aengine+sort%3Aupdated-asc)

If you come across a bug that is unrelated to the engine, remove the `engine` label and leave a comment explaining why. That will send it back to triage.

## Design languages

### Material Design

* [Material Design PRs](https://github.com/flutter/flutter/pulls?q=is%3Aopen+is%3Apr+label%3A%22f%3A+material+design%22+sort%3Aupdated-asc)
* [TODAY bugs](https://github.com/flutter/flutter/issues?q=is%3Aopen+is%3Aissue+label%3A%22f%3A+material+design%22+sort%3Aupdated-asc+label%3A%22TODAY%22)
* [Customer blockers](https://github.com/flutter/flutter/issues?q=is%3Aopen+is%3Aissue+label%3A%22f%3A+material+design%22+sort%3Aupdated-asc+label%3A%22severe%3A+customer+blocker%22)
* [Customer critical issues](https://github.com/flutter/flutter/issues?q=is%3Aopen+is%3Aissue+label%3A%22f%3A+material+design%22+sort%3Aupdated-asc+label%3A%22severe%3A+customer+critical%22)
* [Regressions](https://github.com/flutter/flutter/issues?q=is%3Aopen+is%3Aissue+label%3A%22f%3A+material+design%22+sort%3Aupdated-asc+label%3A%22severe%3A+regression%22)
* [Crash bugs](https://github.com/flutter/flutter/issues?q=is%3Aopen+is%3Aissue+label%3A%22f%3A+material+design%22+sort%3Aupdated-asc+label%3A%22severe%3A+crash%22)
* [Popular issues](https://github.com/flutter/flutter/issues?q=is%3Aopen+is%3Aissue+label%3A%22f%3A+material+design%22+sort%3Areactions-%2B1-desc+-label%3A%22severe%3A+new+feature%22)
* [Popular feature requests](https://github.com/flutter/flutter/issues?q=is%3Aopen+is%3Aissue+label%3A%22f%3A+material+design%22+sort%3Areactions-%2B1-desc+label%3A%22severe%3A+new+feature%22)
* [Old issues](https://github.com/flutter/flutter/issues?q=is%3Aopen+is%3Aissue+label%3A%22f%3A+material+design%22+sort%3Aupdated-asc)

If you come across a bug that is unrelated to material design, remove the `f: material design` label and leave a comment explaining why. That will send it to the framework triage process.

### Cupertino

* [Cupertino PRs](https://github.com/flutter/flutter/pulls?q=is%3Aopen+is%3Apr+label%3A%22f%3A+cupertino%22+sort%3Aupdated-asc)
* [TODAY bugs](https://github.com/flutter/flutter/issues?q=is%3Aopen+is%3Aissue+label%3A%22f%3A+cupertino%22+sort%3Aupdated-asc+label%3A%22TODAY%22)
* [Customer blockers](https://github.com/flutter/flutter/issues?q=is%3Aopen+is%3Aissue+label%3A%22f%3A+cupertino%22+sort%3Aupdated-asc+label%3A%22severe%3A+customer+blocker%22)
* [Customer critical issues](https://github.com/flutter/flutter/issues?q=is%3Aopen+is%3Aissue+label%3A%22f%3A+cupertino%22+sort%3Aupdated-asc+label%3A%22severe%3A+customer+critical%22)
* [Regressions](https://github.com/flutter/flutter/issues?q=is%3Aopen+is%3Aissue+label%3A%22f%3A+cupertino%22+sort%3Aupdated-asc+label%3A%22severe%3A+regression%22)
* [Crash bugs](https://github.com/flutter/flutter/issues?q=is%3Aopen+is%3Aissue+label%3A%22f%3A+cupertino%22+sort%3Aupdated-asc+label%3A%22severe%3A+crash%22)
* [Popular issues](https://github.com/flutter/flutter/issues?q=is%3Aopen+is%3Aissue+label%3A%22f%3A+cupertino%22+sort%3Areactions-%2B1-desc+-label%3A%22severe%3A+new+feature%22)
* [Popular feature requests](https://github.com/flutter/flutter/issues?q=is%3Aopen+is%3Aissue+label%3A%22f%3A+cupertino%22+sort%3Areactions-%2B1-desc+label%3A%22severe%3A+new+feature%22)
* [Old issues](https://github.com/flutter/flutter/issues?q=is%3Aopen+is%3Aissue+label%3A%22f%3A+cupertino%22+sort%3Aupdated-asc)

If you come across a bug that is unrelated to cupertino, remove the `f: cupertino` label and leave a comment explaining why. That will send it to the framework triage process.

## Framework (excluding Material Design and Cupertino)

* [Framework PRs](https://github.com/flutter/flutter/pulls?q=is%3Aopen+is%3Apr+label%3Aframework+-label%3A%22f%3A+material+design%22+-label%3A%22f%3A+cupertino%22+sort%3Aupdated-asc)
* [TODAY bugs](https://github.com/flutter/flutter/issues?q=is%3Aopen+is%3Aissue+label%3Aframework+-label%3A%22f%3A+material+design%22+-label%3A%22f%3A+cupertino%22+sort%3Aupdated-asc+label%3A%22TODAY%22)
* [Customer blockers](https://github.com/flutter/flutter/issues?q=is%3Aopen+is%3Aissue+label%3Aframework+-label%3A%22f%3A+material+design%22+-label%3A%22f%3A+cupertino%22+sort%3Aupdated-asc+label%3A%22severe%3A+customer+blocker%22)
* [Customer critical issues](https://github.com/flutter/flutter/issues?q=is%3Aopen+is%3Aissue+label%3Aframework+-label%3A%22f%3A+material+design%22+-label%3A%22f%3A+cupertino%22+sort%3Aupdated-asc+label%3A%22severe%3A+customer+critical%22)
* [Regressions](https://github.com/flutter/flutter/issues?q=is%3Aopen+is%3Aissue+label%3Aframework+-label%3A%22f%3A+material+design%22+-label%3A%22f%3A+cupertino%22+sort%3Aupdated-asc+label%3A%22severe%3A+regression%22)
* [Crash bugs](https://github.com/flutter/flutter/issues?q=is%3Aopen+is%3Aissue+label%3Aframework+-label%3A%22f%3A+material+design%22+-label%3A%22f%3A+cupertino%22+sort%3Aupdated-asc+label%3A%22severe%3A+crash%22)
* [Popular issues](https://github.com/flutter/flutter/issues?q=is%3Aopen+is%3Aissue+label%3Aframework+-label%3A%22f%3A+material+design%22+-label%3A%22f%3A+cupertino%22+sort%3Areactions-%2B1-desc+-label%3A%22severe%3A+new+feature%22)
* [Popular feature requests](https://github.com/flutter/flutter/issues?q=is%3Aopen+is%3Aissue+label%3Aframework+-label%3A%22f%3A+material+design%22+-label%3A%22f%3A+cupertino%22+sort%3Areactions-%2B1-desc+label%3A%22severe%3A+new+feature%22)
* [Old issues](https://github.com/flutter/flutter/issues?q=is%3Aopen+is%3Aissue+label%3Aframework+-label%3A%22f%3A+material+design%22+-label%3A%22f%3A+cupertino%22+sort%3Aupdated-asc)

If you come across a bug that is unrelated to the framework, remove the `framework` label and leave a comment explaining why. That will send it back to triage.

## Tool

* [Tool PRs](https://github.com/flutter/flutter/pulls?q=is%3Aopen+is%3Apr+label%3Atool+sort%3Aupdated-asc)
* [TODAY bugs](https://github.com/flutter/flutter/issues?q=is%3Aopen+is%3Aissue+label%3Atool+sort%3Aupdated-asc+label%3A%22TODAY%22)
* [Customer blockers](https://github.com/flutter/flutter/issues?q=is%3Aopen+is%3Aissue+label%3Atool+sort%3Aupdated-asc+label%3A%22severe%3A+customer+blocker%22)
* [Customer critical issues](https://github.com/flutter/flutter/issues?q=is%3Aopen+is%3Aissue+label%3Atool+sort%3Aupdated-asc+label%3A%22severe%3A+customer+critical%22)
* [Regressions](https://github.com/flutter/flutter/issues?q=is%3Aopen+is%3Aissue+label%3Atool+sort%3Aupdated-asc+label%3A%22severe%3A+regression%22)
* [Crash bugs](https://github.com/flutter/flutter/issues?q=is%3Aopen+is%3Aissue+label%3Atool+sort%3Aupdated-asc+label%3A%22severe%3A+crash%22)
* [Popular issues](https://github.com/flutter/flutter/issues?q=is%3Aopen+is%3Aissue+label%3Atool+sort%3Areactions-%2B1-desc+-label%3A%22severe%3A+new+feature%22)
* [Popular feature requests](https://github.com/flutter/flutter/issues?q=is%3Aopen+is%3Aissue+label%3Atool+sort%3Areactions-%2B1-desc+label%3A%22severe%3A+new+feature%22)
* [Old issues](https://github.com/flutter/flutter/issues?q=is%3Aopen+is%3Aissue+label%3Atool+sort%3Aupdated-asc)

If you come across a bug that is unrelated to the tool, remove the `tool` label and leave a comment explaining why. That will send it back to triage.

## Web

* [Web PRs](https://github.com/flutter/flutter/pulls?q=is%3Aopen+is%3Apr+label%3A"☸+platform-web"+sort%3Aupdated-asc)
* [TODAY bugs](https://github.com/flutter/flutter/issues?q=is%3Aopen+is%3Aissue+label%3A"☸+platform-web"+sort%3Aupdated-asc+label%3A%22TODAY%22)
* [Customer blockers](https://github.com/flutter/flutter/issues?q=is%3Aopen+is%3Aissue+label%3A"☸+platform-web"+sort%3Aupdated-asc+label%3A%22severe%3A+customer+blocker%22)
* [Customer critical issues](https://github.com/flutter/flutter/issues?q=is%3Aopen+is%3Aissue+label%3A"☸+platform-web"+sort%3Aupdated-asc+label%3A%22severe%3A+customer+critical%22)
* [Regressions](https://github.com/flutter/flutter/issues?q=is%3Aopen+is%3Aissue+label%3A"☸+platform-web"+sort%3Aupdated-asc+label%3A%22severe%3A+regression%22)
* [Crash bugs](https://github.com/flutter/flutter/issues?q=is%3Aopen+is%3Aissue+label%3A"☸+platform-web"+sort%3Aupdated-asc+label%3A%22severe%3A+crash%22)
* [Popular issues](https://github.com/flutter/flutter/issues?q=is%3Aopen+is%3Aissue+label%3A"☸+platform-web"+sort%3Areactions-%2B1-desc+-label%3A%22severe%3A+new+feature%22)
* [Popular feature requests](https://github.com/flutter/flutter/issues?q=is%3Aopen+is%3Aissue+label%3A"☸+platform-web"+sort%3Areactions-%2B1-desc+label%3A%22severe%3A+new+feature%22)
* [Old issues](https://github.com/flutter/flutter/issues?q=is%3Aopen+is%3Aissue+label%3A"☸+platform-web"+sort%3Aupdated-asc)

If you come across a bug that is unrelated to the Web backend, remove the `☸ platform-web` label and leave a comment explaining why. That will send it back to triage.

## Plugins, Packages, and Ecosystem

### Plugins

* [Plugins PRs](https://github.com/flutter/plugins/pulls?q=is%3Apr+is%3Aopen+sort%3Aupdated-asc)
* [TODAY bugs](https://github.com/flutter/flutter/issues?q=is%3Aopen+is%3Aissue+label%3Aplugin+sort%3Aupdated-asc+label%3A%22TODAY%22)
* [Customer blockers](https://github.com/flutter/flutter/issues?q=is%3Aopen+is%3Aissue+label%3Aplugin+sort%3Aupdated-asc+label%3A%22severe%3A+customer+blocker%22)
* [Customer critical issues](https://github.com/flutter/flutter/issues?q=is%3Aopen+is%3Aissue+label%3Aplugin+sort%3Aupdated-asc+label%3A%22severe%3A+customer+critical%22)
* [Regressions](https://github.com/flutter/flutter/issues?q=is%3Aopen+is%3Aissue+label%3Aplugin+sort%3Aupdated-asc+label%3A%22severe%3A+regression%22)
* [Crash bugs](https://github.com/flutter/flutter/issues?q=is%3Aopen+is%3Aissue+label%3Aplugin+sort%3Aupdated-asc+label%3A%22severe%3A+crash%22)
* [Popular issues](https://github.com/flutter/flutter/issues?q=is%3Aopen+is%3Aissue+label%3Aplugin+sort%3Areactions-%2B1-desc+-label%3A%22severe%3A+new+feature%22)
* [Popular feature requests](https://github.com/flutter/flutter/issues?q=is%3Aopen+is%3Aissue+label%3Aplugin+sort%3Areactions-%2B1-desc+label%3A%22severe%3A+new+feature%22)
* [Old issues](https://github.com/flutter/flutter/issues?q=is%3Aopen+is%3Aissue+label%3Aplugin+sort%3Aupdated-asc)

### Packages

* [Packages PRs](https://github.com/flutter/packages/pulls?q=is%3Apr+is%3Aopen+sort%3Aupdated-asc)
* [TODAY bugs](https://github.com/flutter/flutter/issues?q=is%3Aopen+is%3Aissue+label%3Apackage+sort%3Aupdated-asc+label%3A%22TODAY%22)
* [Customer blockers](https://github.com/flutter/flutter/issues?q=is%3Aopen+is%3Aissue+label%3Apackage+sort%3Aupdated-asc+label%3A%22severe%3A+customer+blocker%22)
* [Customer critical issues](https://github.com/flutter/flutter/issues?q=is%3Aopen+is%3Aissue+label%3Apackage+sort%3Aupdated-asc+label%3A%22severe%3A+customer+critical%22)
* [Regressions](https://github.com/flutter/flutter/issues?q=is%3Aopen+is%3Aissue+label%3Apackage+sort%3Aupdated-asc+label%3A%22severe%3A+regression%22)
* [Crash bugs](https://github.com/flutter/flutter/issues?q=is%3Aopen+is%3Aissue+label%3Apackage+sort%3Aupdated-asc+label%3A%22severe%3A+crash%22)
* [Popular issues](https://github.com/flutter/flutter/issues?q=is%3Aopen+is%3Aissue+label%3Apackage+sort%3Areactions-%2B1-desc+-label%3A%22severe%3A+new+feature%22)
* [Popular feature requests](https://github.com/flutter/flutter/issues?q=is%3Aopen+is%3Aissue+label%3Apackage+sort%3Areactions-%2B1-desc+label%3A%22severe%3A+new+feature%22)
* [Old issues](https://github.com/flutter/flutter/issues?q=is%3Aopen+is%3Aissue+label%3Apackage+sort%3Aupdated-asc)

If you come across a bug that is unrelated to our plugins or packages, remove the `plugin` or `package` label and leave a comment explaining why. That will send it back to triage.

## Desktop

* [Desktop PRs on the engine](https://github.com/flutter/engine/pulls?q=is%3Aopen+is%3Apr+label%3A%22affects%3A+desktop%22+sort%3Aupdated-asc)
* [Desktop PRs on the framework](https://github.com/flutter/flutter/pulls?q=is%3Aopen+is%3Apr+label%3A%22a%3A+desktop%22+sort%3Aupdated-asc)
* [TODAY bugs](https://github.com/flutter/flutter/issues?q=is%3Aopen+is%3Aissue+label%3A%22a%3A+desktop%22+sort%3Aupdated-asc+label%3A%22TODAY%22)
* [Customer blockers](https://github.com/flutter/flutter/issues?q=is%3Aopen+is%3Aissue+label%3A%22a%3A+desktop%22+sort%3Aupdated-asc+label%3A%22severe%3A+customer+blocker%22)
* [Customer critical issues](https://github.com/flutter/flutter/issues?q=is%3Aopen+is%3Aissue+label%3A%22a%3A+desktop%22+sort%3Aupdated-asc+label%3A%22severe%3A+customer+critical%22)
* [Regressions](https://github.com/flutter/flutter/issues?q=is%3Aopen+is%3Aissue+label%3A%22a%3A+desktop%22+sort%3Aupdated-asc+label%3A%22severe%3A+regression%22)
* [Crash bugs](https://github.com/flutter/flutter/issues?q=is%3Aopen+is%3Aissue+label%3A%22a%3A+desktop%22+sort%3Aupdated-asc+label%3A%22severe%3A+crash%22)
* [Popular issues](https://github.com/flutter/flutter/issues?q=is%3Aopen+is%3Aissue+label%3A%22a%3A+desktop%22+sort%3Areactions-%2B1-desc+-label%3A%22severe%3A+new+feature%22)
* [Popular feature requests](https://github.com/flutter/flutter/issues?q=is%3Aopen+is%3Aissue+label%3A%22a%3A+desktop%22+sort%3Areactions-%2B1-desc+label%3A%22severe%3A+new+feature%22)
* [Old issues](https://github.com/flutter/flutter/issues?q=is%3Aopen+is%3Aissue+label%3A%22a%3A+desktop%22+sort%3Aupdated-asc)

If you come across a bug that is unrelated to desktop app development, remove the `a: desktop` label and leave a comment explaining why. That will send it back to triage.

## Adding a new team

To add a new team:

1. Create a new section above, copying-and-pasting one of the existing sections, and update it accordingly.
2. Update all the parts of this wiki page that are labeled (in the source) with `<!--ALL-LABELS-->`.
