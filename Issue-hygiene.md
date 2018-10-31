This page describes how Flutter uses GitHub issues to track work on the project.

## Overview

We use three issue trackers: the [main one on flutter/flutter](https://github.com/flutter/flutter/issues), one for [the flutter.io Website, on flutter/website](https://github.com/flutter/website/issues), and one for [the IntelliJ and Android Studio plugins, on flutter/flutter-intellij](https://github.com/flutter/flutter-intellij/issues).

This page mostly talks about how we handle things for the flutter/flutter issue tracker.


## Labels

We use [many labels](https://github.com/flutter/flutter/labels).

See also:

 * [[Triage]], which describes how we go through bugs and make sure they are sorted accordingly.


## Prioritization

### Customers

Some teams using Flutter have a special relationship with the Flutter team.

We provide such customers with a label in our GitHub issue tracker, and
encourage them to mark bugs as "customer blocker" (for the most serious bugs)
or "customer critical" (for still important but not quite as serious bugs)
if they are very important and deserve immediate attention.

Each week, we ensure that bugs marked with these labels have someone assigned
to them so that they are likely to get traction (this process is described
in the second section of the page on [[Triage]]).

We also have two special customer labels. The "customer: crowd" label is used
to represent bugs that are affecting large numbers of people; during initial
[[Triage]], high-profile bugs get labeled in this way to bring them to the 
attention of the engineering team. The "customer: product" label is used by
the team to bring issues that product management and senior leads want resolved
to the attention of the engineering team.

Be wary in marking bugs as "customer blocker". We have finite resources,
and several customers. If you have one "blocker" bug, then we have a clear
sign of what to look at. If you have twenty, it will be difficult for us
to know where to start.

See also:

 * [Current customer blockers](https://github.com/flutter/flutter/issues?utf8=%E2%9C%93&q=is%3Aissue+is%3Aopen+label%3A%22severe%3A+customer+blocker%22)

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
for advice on how to that successfully.)

### Buckets

Bugs assigned to a specific bucket are bugs we are intended to address by a particular
date. Any bug assigned to a bucket should have a specific dedicated assignee as well.

Contributors working to address an issue should self-assign the issue to a relevant 
bucket to indicate to others when you anticipate completing the issue. Buckets are a
statement of what we anticipate doing and when, and thus concrete; they are not
aspirational, in that simply assigning an issue to a bucket without the commitment of
its assignee will not further work on the issue on its own. (See “Assigning Issues”, 
below.)


### Other milestones

The "Scheduled customer work" milestone indicates issues that we are intending to fix
soon but for which we don't yet have an owner. The "Declined customer request" milestone
indicates an issue that we do not intend to address in the near future.

Each week, we check any bug with the "customer: blocker" or "customer: critical" labels.
If we can find an owner for such a bug right away, it will be assigned a bucket milestone
and given an explicit assignee. If we cannot, then it will be put into either the
"Scheduled customer work" milestone or the "Declined customer request" milestone.

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

When a bug is assigned, it should also have a milestone.


## File bugs for everything

File bugs for anything that you come across that needs doing. When you
implement something but know it's not complete, file bugs for what you
haven't done. That way, we can keep track of what still needs doing.


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

If the milestone is "Scheduled customer work", then we are likely to address
the issue in the near term, we just need to find time. Please hold on.

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