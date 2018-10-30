This page describes how Flutter uses GitHub issues to track work on the project.

## Overview

We use three issue trackers: the [main one on flutter/flutter](https://github.com/flutter/flutter/issues), one for [the flutter.io Website, on flutter/website](https://github.com/flutter/website/issues), and one for [the IntelliJ and Android Studio plugins, on flutter/flutter-intellij](https://github.com/flutter/flutter-intellij/issues).

This page mostly talks about how we handle things for the flutter/flutter issue tracker.


## Labels

We use [many labels](https://github.com/flutter/flutter/labels).

See also: [[Triage]], which describes how we go through bugs and make sure they are sorted accordingly.


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


## Prioritization

Some teams using Flutter have a special relationship with the Flutter team.

We provide such customers with a label in our GitHub issue tracker, and
encourage them to mark bugs as "customer blocker" (for the most serious bugs)
or "customer critical" (for still important but not quite as serious bugs)
if they are very important and deserve immediate attention.

Each week, we ensure that bugs marked with these labels have someone assigned
to them so that they are likely to get traction (this process is described
in the second section of the page on [[Triage]]).

Be wary in marking bugs as "customer blocker". We have finite resources,
and several customers. If you have one "blocker" bug, then we have a clear
sign of what to look at. If you have twenty, it will be difficult for us
to know where to start.

Current customer blockers: https://github.com/flutter/flutter/issues?utf8=%E2%9C%93&q=is%3Aissue+is%3Aopen+label%3A%22severe%3A+customer+blocker%22


## Assigning bugs

Bugs are typically self-assigned. Only assign a bug to someone else if
they have explicitly volunteered to do the task.

Only assign a bug to yourself when you are actively working on it. If
you're not working on it, leave it unassigned. Don't assign bugs to
people unless you know they are going to work on it. If you find
yourself with bugs assigned that you are not going to be working on in
the very near future, unassign the bug so that other people feel
empowered to work on them.

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
