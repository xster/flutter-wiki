This page describes how Flutter uses GitHub issues to track work on the project.

## Overview

We use three issue trackers: the [main one on flutter/flutter](https://github.com/flutter/flutter/issues), one for [the flutter.dev Website, on flutter/website](https://github.com/flutter/website/issues), and one for [the IntelliJ and Android Studio plugins, on flutter/flutter-intellij](https://github.com/flutter/flutter-intellij/issues).

This page mostly talks about how we handle things for the flutter/flutter issue tracker.


## Issue philosophy

We assume that Flutter, like all non-trivial software, has an infinite number of bugs. The issue tracker contains the list of bugs that we are very lucky to have had reported by our generous community.

Within the bug database we try to make sure each issue is actionable and discoverable. We do this by carefully updating the issue subject line, making sure every issue has steps to reproduce, and using labels to categorize the issue in ways that can be found by GitHub search.


## Labels

We use [many labels](https://github.com/flutter/flutter/labels).

The `severe:` prefix indicates labels regarding a level of severity (e.g. regressions, new features, crashes). Severity in and of itself does not say how fast we will fix the bug; rather it provides clues as to the nature of the defect, just as other labels do. A bug may have the `severe: crash` label, but e.g. if it relates to the `flutter` tool crashing when run with the system date set to 1998, we are not likely to consider it a high priority.

The `TODAY` label indicates a high-priority issue such as a build break, regression or failure in existing features that would keep us from shipping the current build. We look at issues with the `TODAY` label frequently to ensure that each has an owner and is being worked on.

The `RELEASE BLOCKER` label is similar. It indicates that the bug should be hot-fixed on any upcoming stable releases. Since the Open/Closed status of a bug relates to the master branch, not to the beta branch, it will be common for this label to be applied to closed bugs. The bug is removed as part of the [release process](https://github.com/flutter/flutter/wiki/Release-process) when the issue is hot-fixed.

_See also: [[Triage]], which describes how we go through bugs and make sure they are sorted accordingly._


### Adding labels

Labels are more or less free, so we can add them pretty easily. Please mention it to other team members first, so that they know what you are planning and can give feedback. Please make sure labels use a consistent color and naming scheme (e.g. all the framework-related labels are blue and start with `f:`).

Labels should be used for adding information to a bug. If you plan to use a label to find all instances of a particular topic (e.g. finding all PRs where someone wrote a design doc), be aware that there's no way to force people to label issues or PRs. You can, however, rely on automation to do it, for example you could write a script that labels all PRs that affect the framework.

## Prioritization

_See also: [[What should I work on?]]_

### Customers

Some teams using Flutter have a special relationship with the Flutter team
(e.g. they're collaborating with us on a new feature, or they're working
with us on a product demo for an upcoming event). This is usually a fairly
short-term arrangement for a specific business purpose.

We provide such customers with a label in our GitHub issue tracker, and
encourage them to mark bugs as "customer blocker" (for the most serious bugs)
or "customer critical" (for still important but not quite as serious bugs)
if they are very important and deserve immediate attention.

(Bugs marked "customer blocker" or "customer critical" must have the corresponding
customer label as well, so that we know who is actually asking for the issue.)

Each week, we look at all the bugs marked with the "customer blocker" and "customer critical"
labels, and see if we can address them ahead of other bugs (though there is no guarantee
that we will; it mostly depends on how close a relationship we have with the relevant customer).
This process is described in the "critical triage" section of the page on [[Triage]].

Be wary in marking bugs as "customer blocker". We have finite resources,
and several customers. If you have one "blocker" bug, then we have a clear
sign of what to look at. If you have twenty, it will be difficult for us
to know where to start.

_See also: [Current customer blockers](https://github.com/flutter/flutter/issues?utf8=%E2%9C%93&q=is%3Aissue+is%3Aopen+label%3A%22severe%3A+customer+blocker%22),
[Current customer critical issues](https://github.com/flutter/flutter/issues?utf8=%E2%9C%93&q=is%3Aissue+is%3Aopen+label%3A%22severe%3A+customer+critical%22)._

#### Coordinating between bug systems

Some customers have their own separate bug systems, in which they track Flutter
issues. We consider our GitHub issue list to be canonical. However, if there
is a link from the issue in our bug system to the customer's issue in their bug
system, and we have been granted access, we will follow that link and may
communicate in that separate bug system when attempting to track down the issue.

#### Special customer labels

We have two special customer labels which are used exclusively with the "critical"
and "blocker" labels.

The "customer: product" label is used to bring issues that product management
and senior leads want resolved to the attention of the appropriate engineering
team.

The "customer: crowd" label is used to represent bugs that are affecting large
numbers of people; during initial [[Triage]], high-profile bugs get labeled in
this way to bring them to the attention of the engineering team. "Large numbers"
is a judgement call. If dozens of people independently run into the same issue
and file a bug and we end up realizing that they're all duplicates of each other,
then that would be a good candidate. On the other hand, if there is an active
campaign to get people to comment on a bug, then it's probably not legitimately
a "customer: crowd" bug, because people generally report bugs without having to
be convinced to do so.

In general, a bug should only be marked "customer: crowd" "customer blocker" if it
is so bad that it is literally causing large numbers of people to consider changing
careers. Similarly, a bug should only be marked "customer: crowd" "customer critical"
if people are threatening to never upgrade to the next release because of this bug.

### Thumbs-up reactions

To vote on an issue, use the "Thumbs-up" emoji to react to the issue.

When examining issues, we use the number of thumbs-up reactions to an issue
as a guide to its priority.

See also:

 * [All open issues sorted by thumbs-up](https://github.com/flutter/flutter/issues?q=is%3Aissue+is%3Aopen+sort%3Areactions-%2B1-desc)

 * [Feature requests by thumbs-up](https://github.com/flutter/flutter/issues?q=is%3Aissue+is%3Aopen+sort%3Areactions-%2B1-desc+label%3A%22severe%3A+new+feature%22)

 * [Bugs by thumbs-up](https://github.com/flutter/flutter/issues?utf8=%E2%9C%93&q=is%3Aissue+is%3Aopen+sort%3Areactions-%2B1-desc+-label%3A%22severe%3A+new+feature%22+)

We ignore other emoji reactions.


## Milestones

We use [several milestones](https://github.com/flutter/flutter/milestones).

We mostly use milestones to handle broad categorization and to indicate specific intentions.
This is separate from bug prioritization, which is described next.

### Goals, Stretch Goals, Future

The first set of milestones help us categorize unassigned work. In general, team
members look to fix bugs in the "Goals" milestone. Bugs in "Stretch Goals" are
things that we're unlikely to work on in the coming year, and bugs in "Future"
are things that we're unlikely to work on at all. However, we accept contributions
regardless of the milestone, so if you care about an issue marked "Future", do
not hesitate to contribute a PR to address it! (See our
[contribution guide](https://github.com/flutter/flutter/blob/master/CONTRIBUTING.md)
for advice on how to do that successfully.)

### Months (aka release milestones)

Bugs assigned to a specific month (e.g. "February 2021") are bugs we are intended to
address before the end of that month. This usually means that the bug fix will be present
in the release made in the next month. For example, if a bug has the milestone "February
2021", it means that we expect the relevant PR to land on or before February 28th 2021,
and that the beta release made in March 2021 will include the fix.

See the [[Release process]] page for more details about our release schedule.

Any bug assigned to a month should have a specific dedicated assignee as well.

Contributors working to address an issue should self-assign the issue to a relevant 
bucket to indicate to others when you anticipate completing the issue. Release milestones are a
statement of what we anticipate doing and when, and thus concrete; they are not
aspirational, in that simply assigning an issue to a bucket without the commitment of
its assignee will not further work on the issue on its own. (See “Assigning Issues”, 
below.)

In some cases, a month may have a feature-specific milestone in addition to the general month 
milestone. This is for tracking burndowns in [ZenHub](https://www.zenhub.com/); some subteams
are using burndown charts against specific completion milestones and need their own milestone
for tracking. Other rules for release milestones still apply.

### Near-term Goals

This special milestone is sometimes used as a stepping stone from Goals to a specific month. A subteam
will triage bugs in the Goals milestone, determine that they should work a specific subset of
issues, and put them in Near-term Goals. Then, later, when they are scheduling work, they will
move bugs from Near-term Goals to a specific month, and assign a specific engineer.

### Other milestones

The "Unassigned customer work" milestone indicates issues that we are intending to fix
soon but for which we don't yet have an owner. The "Declined customer request" milestone
indicates an issue that we do not intend to address on the timescale requested (typically
this means not for 6+ months).

Each week, we check any bug with the "customer: blocker" or "customer: critical" labels.
If we can find an owner for such a bug right away, it will be assigned a bucket milestone
and given an explicit assignee. If we cannot, then it will be put into either the
"Unassigned customer work" milestone or the "Declined customer request" milestone.

If you are a customer and your bug is placed in the "Declined customer request"
milestone, please escalate with your team contact.


## Assigning Issues

Issues are typically self-assigned. Only assign a bug to someone else if
they have explicitly volunteered to do the task.

Only assign a bug to yourself when you are actively working on it. If
you're not working on it, leave it unassigned. Don't assign bugs to
people unless you know they are going to work on it. If you find
yourself with bugs assigned that you are not going to be working on in
the very near future, unassign the bug so that other people feel
empowered to work on them.

_Do_ assign a bug to yourself if you are working on it! This is how
people can figure out what is happening. It also prevents duplicate
work where two people try to fix the same issue at once.

You may hear team members refer to "licking the cookie". Assigning a
bug to yourself, or otherwise indicating that you will work on it,
tells others on the team to not fix it. If you then don't work on it
straight away, you are acting like someone who has taken a cookie,
licked it to be unappetizing to other people, and then not eaten it right
away. By extension, "unlicking the cookie" means indicating to the
rest of the team that you are not actually going to work on the bug
right away after all, e.g. by unassigning the bug from yourself.

When a bug is assigned, it should also have a specific release milestone (one of the
milestones that refers to a specific month). Be conservative in assigning the
milestone. It is infinitely preferable for a bug to be fixed earlier than expected
than it is for it to slip from release to release.


## File bugs for everything

File bugs for anything that you come across that needs doing. When you
implement something but know it's not complete, file bugs for what you
haven't done. That way, we can keep track of what still needs doing.

### Exceptions

Do _not_ file bugs that meet the following criteria:

- Asking meta-questions like "why was bug #XYZ closed?" Instead, post
  on the original issue or raise the actual problem that is still not
  resolved.
- Intentional duplicates like  "This is the same as bug #ABC but that 
  one is not getting enough attention." Instead, upvote the original
  issue or add a comment that provides new details that are not already
  captured or (best of all) assign it to yourself and start working on it!

## Do not add "me too" or "same" comments to bugs

The Flutter team prioritizes issues in part based on the number of +1 (thumbs
up) reactions on the top level comment of the bug.  Adding comments like "me
too" or "same here" is generally distracting and makes it harder to find
other more meaningful content in the bug. If you have no new details to add,
consider just thumbs up-ing the issue.  If you wish to subscribe to the issue,
click the "subscribe" button in the right hand column of the GitHub UI.

## When will my bug be fixed?

To determine when a bug will be fixed, look at the milestone.

If it has no milestone, it may not yet have been triaged.

If the milestone is "Stretch Goals", "Future", or "Declined customer
request", we are not planning on working on the bug. We apologize. We
are but a small team with an infinite amount of work, so we have to
set boundaries. Consider escalating the issue, as described below.

If the milestone is "Goals", then we are not planning on working on it
any time soon, but we agree that it's something we should work on in
the coming years. Consider escalating the issue, as described below, to
raise its priority further.

If the milestone is "Unassigned customer work" or "Near-term goals",
then we are likely to address the issue in the near term, we just
need to find time. Please hold on.

Otherwise, the milestone should be a precise description of when we expect
the work to be done by. It may not be accurate; we often end up slipping
a fix into the next release. Engineering is not always an easily scheduled art.

### Escalating an issue

If you have a relationship with the Flutter team, raise the issue with
your contact.

If you don't, consider finding like-minded developers to either implement
the feature as a team, or to fund hiring someone to work on the feature,
or to mark the issue with a thumbs-up reaction. We regularly look through
all issues sorted by the number of thumbs-up reactions to determine the
relative priority of upcoming work, so that is one way to change our mind.

## Locking an issue

Closed issues that haven't received any activity in a [few weeks](https://github.com/flutter/flutter/blob/master/.github/lock.yml#L4)
are automatically locked by a [bot](https://github.com/apps/lock). This is
done to encourage developers to file new bugs, instead of piling comments
on old ones.

Under normal circumstances, issues should not regularly be locked. The most
common reasons for manually locking an issue are:

1. The issue is well understood by the engineers working on it,
   is believed to be appropriately prioritized, has has a clear 
   path to being fixed, and is otherwise attracting
   a lot of off-topic or distracting comments like "me too" or
   "when will this be fixed" or "I have a similar issue that might
   or might not be the same as this one".
2. The issue has been closed as fixed, and is attracting a lot of
   off-topic comments that should either be filed as independent issues
   or posted on a site like StackOverflow.com as a question.

In these states, the issue is generally considered to be well
understood, no longer in need of reproduction test cases, and
either actively being worked on or already fixed on master.

If you are concerned that such an issue is not receiving its due
attention, see Escalating an Issue, described above. If you are
not already a contributor but would like to work on that issue,
consider reaching out on an appropriate [chat](https://github.com/flutter/flutter/wiki/Chat).

If you have a similar issue and are not sure if it is the same,
it is fine to file a new issue and linking it to the other issue.
Please avoid intentionally filing duplicates.

Very rarely, an issue gets locked because discussion has become 
unproductive and has repeatedly violated the [Code of Conduct](https://github.com/flutter/flutter/blob/master/CODE_OF_CONDUCT.md).

## Provide reduced test cases

To debug a problem, we will need to be able to reproduce it. The best way
to help us do that is to provide code, licensed according to [the BSD license
used by Flutter](https://github.com/flutter/flutter/blob/master/LICENSE), that
has been reduced as far as possible (such that removing anything further stops
showing the bug). Attach such a file or files to the issue itself.

For legal reasons, we cannot debug problems that require looking at proprietary
code or, generally, code that is not publicly available.

## Avoid posting screenshots of text

If you want to show code, quote someone, or show a string of text that does
not render properly with Flutter, please avoid sharing it via an image or
screenshot. Text in images cannot be copied, and cannot be automatically
translated via services like Google Translate. This makes it harder for team
members who do not speak that language to participate in the issue.

It is perfectly fine to share a screenshot of text rendering invalidly, but
also include the actual string or character(s) that lead to it so that they
can be copied and pasted into a test case.

## Consider posting issues in English

If you are able to read and write English clearly, consider posting your issue
in English, even if it is about a language specific issue (like the way text
renders in some non-English language).

It is fine to post issues in languages other than English, but consider that
many readers will rely on automatic translation services to read your issue.
Please avoid using screenshots in languages other than English, as services like
Google Translate will not translate the text in images, and the pool of people
able to assist you will be reduced.