This page covers how to land a PR and other aspects of writing code for
Flutter other than the actual writing of the code. For guidance on
designing APIs, documenting, and formatting your code, see the
[[Style guide for Flutter repo]] document.

## Overview

The general process for submitting code to a Flutter repository is as follows:

1. Fork the repository on GitHub (see the
   [contributing guide](https://github.com/flutter/flutter/blob/master/CONTRIBUTING.md)
   for advice on doing this and in general setting up your development environment).

2. If there is not already an issue covering the work you are interested in doing,
   and if you think the work you are doing is likely to be a non-trivial effort
   (e.g. you are creating a new widget, or otherwise introducing a new API surface,
   or intending to make a potentially breaking change, or refactoring a library),
   then file a new bug to describe the issue you are addressing.

3. If the work you are doing is likely to be a non-trivial effort (see above), then
   discuss your design on the issue. You may find it useful to create a Google Doc to
   solicit feedback. You may wish to e-mail the mailing list, or discuss the topic
   on the contributors Gitter channel. The more buy-in you get from the rest of the
   team (especially the relevant leads), the easier the rest of the process will be.

4. Create a branch on your GitHub fork of the repository, and implement your change.
   Make sure it is tested. Consider using the code coverage tools to check that all
   your new code is covered by tests (see [[Test coverage for package:flutter]]).

5. Submit this branch as a PR to the relevant Flutter repository.

6. Get your code reviewed (see below). You should probably reach out to the relevant
   expert(s) for the areas you touched and ask them to review your PR directly.
   GitHub sometimes recommends specific reviewers; if you're not sure who to ask,
   that's probably a good place to start.

7. Make sure your PR passes all the pre-commit tests. Consider running some of the
   post-commit tests locally (see the
   [devicelab](https://github.com/flutter/flutter/blob/master/dev/devicelab/README.md)
   directory).

   Wait for Cirrus to give the green light before merging a PR. Cirrus
   runs a bunch of precommit checks (e.g. see the tests for the
   [framework](https://github.com/flutter/flutter/blob/master/dev/bots/test.dart),
   or the [engine](https://github.com/flutter/engine/blob/master/ci/build.sh)).
   These checks include checks on comments, so make sure you wait for the
   green light even if your patch is _obviously_ fine!

   The `flutter-build` test isn't checking your PR, it's letting you know
   whether the tree itself is passing the tests right now (including post-
   commit tests). If it is red, help out the team in fixing the tree before
   continuing. It is everyone's job to keep the tree green.

   **If the trees or dashboards are showing any regressions, only fixes
   that improve the situation are allowed to go in.**

8. Once everything is green and you have an LGTM, land your patch.

9. Watch the post-commit tests on the dashboard to make sure everything passes. If anything
   goes wrong, revert your patch and study the problem.


## Using git

Assuming your environment has been configured according to the instructions in
[[Setting up the Engine development environment]] or
[[Setting up the Framework development environment]],
follow these steps to start working on a patch:

 * `git fetch upstream`
 * `git checkout upstream/master -b name_of_your_branch`
 * Hack away.
 * `git commit -a -m "<your informative commit message>"`
 * `git push origin name_of_your_branch`

GitHub provides you with a link for submitting the pull request in the message output by `git push`.

Please make sure all your patches have detailed commit messages explaining what the problem was and
what the solution is.

You must complete the
[Contributor License Agreement](https://cla.developers.google.com/clas).
You can do this online, and it only takes a minute.
If you've never submitted code before, you must add your (or your
organization's) name and contact info to the [AUTHORS](AUTHORS) file.


## Getting a code review

Every PR must be code-reviewed before check-in, including things like
rolling a dependency. Getting a review means that a regular Flutter
contributor (someone with commit access) has written a comment saying
"LGTM" on your PR, and you have addressed all their feedback. ("LGTM"
means "Looks Good To Me".)

Code review serves many critical purposes. There's the obvious
purpose: catching errors. Even the most experienced engineers
frequently make errors that are caught by code review. But there are
also many other benefits of code reviews:

 * It spreads knowledge among the team. Since every line of code will
   have been read by two people, it's more likely that once you move
   on, someone else will understand the code.

 * It keeps you honest. Knowing that someone will be reading your
   code, you are less tempted to cut corners and more motivated to
   write code you are proud of.

 * It exposes you to different modes of thinking. Your code reviewer
   has probably not thought about the problem in the same way you
   have, and so may have a fresh perspective and may find you a better
   way to solve the problem.

We recommend you consider
[these suggestions](https://testing.googleblog.com/2017/06/code-health-too-many-comments-on-your.html)
for addressing code review comments on your PR.

If you're working on a big patch, don't hesitate to get reviews early,
before you're ready to check code in. Also, don't hesitate to ask for
multiple people to review your code, and don't hesitate to provide unsolicited
comments on other people's PRs. The more reviews the better.


## How to review code

Reviewers should carefully read the code and make sure they understand
it. A reviewer should check the code for both high level concerns,
such as whether the code's structure makes sense, as well as
readability and adherence to the
[Flutter style guide](https://github.com/flutter/flutter/wiki/Style-guide-for-Flutter-repo).
Use [these best practices](https://mtlynch.io/human-code-reviews-1/)
when reviewing code and providing comments.

Reviewers should not give an LGTM unless the patch has tests that verify
all the affected code, or unless a test would make no sense. If you
review a patch, you are sharing the responsibility for the patch with
its author. You should only give an LGTM if you would feel confident
answering questions about the code.

A reviewer may in some circumstances consider the code satisfactory
without having fully reviewed or understood it. If a reviewer has not
fully reviewed the code, they admit to this by saying "RSLGTM" rather
than just "LGTM". If you feel your code needs a real review, please
find someone to actually review it. ("RSLGTM" means "Rubber Stamp
Looks Good To Me".) If you mark a patch as RSLGTM, you are still
sharing the responsibility for the patch with its author. Reviewing a
patch as RSLGTM should be a rare event.


## Landing a patch

Once you have submitted your patch and received your LGTM, if you do not have commit access to
the repository yet, then wait for one of the project maintainers to submit it for you.

If you do have access, you can just click the green "Merge pull request" button on the GitHub UI
of your pull request. Please squash commits (GitHub does this for you by default normally).


## Emergencies

If you really need to check something in in a hurry, for example
because everything is broken and you can fix it, then pick someone on
the team who you want to have review the code, and then mark the PR as
"TBR" with their name. ("TBR" means "To Be Reviewed".) This is only to
be used in emergencies. (Nobody being around to review your 50,000
line patch at midnight on December 31st is not an emergency!) If
someone marks a patch as TBR and gives your name as the reviewer, you
should review the patch as soon as possible. If a reviewer finds
problems with a patch marked TBR, the issues should be fixed as soon
as possible.


## Regressions

If a check-in has caused a regression on the trunk, roll back the
check-in (even if it isn't yours) unless doing so would take longer
than fixing the bug. When the trunk is broken, it slows down everyone
else on the project.

If things are broken, the priority of everyone on the team should be
helping the team fix the problem. Someone should own the issue, and
they can delegate responsibilities to others on the team. Once the
problem is resolved, write a
[post-mortem](https://github.com/flutter/flutter/wiki/Postmortems).
Postmortems are about documenting what went wrong and how to avoid the
problem (and the entire class of problems like it) from recurring in
the future. Postmortems are emphatically _not_ about assigning blame.

There is no shame in making mistakes.


## Handling breaking changes

We're attempting to stablize the APIs for the
[packages in the SDK](https://github.com/flutter/flutter/tree/master/packages).
To make a change that will require developers to change their code:

 1. Use the `severe: API break` label on the relevant issue.

 2. Send an e-mail to <mailto:flutter-announce@googlegroups.com> to socialize
    your proposed change. The purpose of this e-mail is to see if you can
    get consensus around your change. **You are not telling people that
    the change will happen, you are asking them for permission.**
    The e-mail should include the following:

    - A subject line that clearly summarizes the proposed change and sounds like it
      matters (so that people can spot these e-mails among the noise). Prefix
      the subject line with `[Breaking Change]`.

    - A summary of each change you propose.

    - A brief justification for the change.

    - A link to the relevant issue, and any PRs you may have already posted relating to
      this change.

    - Clear mechanical steps for porting the code from the old form to the new
      form, if possible. If not possible, clear steps for figuring out how to
      port the code.

    - A sincere offer to help port code, which includes the preferred venue for
      contacting the person who made the change.

    - A request that people notify you if this change will be a problem,
      perhaps by discussing the change in the issue tracker on the pull request.

 3. **If folks agree that the benefits of changing the API outweigh the stablity
    costs**, you can proceed with the normal code review process for making
    changes. You should leave some time between steps 2 and 3 (at a bare minimum
    24 hours during the work week so that people in all time zones have had a
    chance to see it, but ideally a week or so).

 4. If you landed a breaking change, add a bullet point to the top section of
    the [Changelog page on the wiki](https://github.com/flutter/flutter/wiki/Changelog),
    describing your change and
    linking to your e-mail in [the mailing list archives](https://groups.google.com/forum/#!forum/flutter-dev).
    To figure out the correct version heading for the changelog run
    `git fetch upstream && flutter --version`. For example, if it says
    "Flutter 0.0.23-pre.10" in the output your changelog entry should be under
    heading "Changes since 0.0.22".

Where possible, even "breaking" changes should be made in a backwards-compatible way,
for example by introducing a new class and marking the old class `@deprecated`. When
doing this, include a description of how to transition in the deprecation notice, for
example:

```dart
@Deprecated('FooInterface has been deprecated because ...; it is recommended that you transition to the new FooDelegate.')
class FooInterface {
  /// ...
}
```

If you use `@deprecated`, make sure to remember to actually remove the feature a few
months later (after the next stable release), do not just leave it forever!


### Google-only responsibilities

If you work for Google, you have the added responsibility of updating Google's
internal copy of Flutter and fixing any broken call-sites reasonably quickly
after merging the upstream change.
