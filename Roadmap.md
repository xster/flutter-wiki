# 2020

In the interest of transparency, we want to share high-level details of our roadmap, so that others can see our priorities and make plans based off the work we are doing.

_Our plans will evolve over time based on customer feedback and new market opportunities. The list here shouldn't be viewed either as exhaustive, nor a promise that we will complete all this work. If you have feedback about what you think we should be working on, we encourage you to get in touch (e.g. by [filing an issue](https://github.com/flutter/flutter/issues/new/choose) or [emailing the flutter-dev mailing list](https://groups.google.com/forum/#!forum/flutter-dev)). Flutter is an open source project, we invite contributions both towards the themes presented below and in other areas._

## Areas of focus

### Web and macOS

At our Flutter Interact event in December 2019, we announced that our support for Web had progressed to beta-level quality. We intend to continue this work with the goal of having Web be supported as an equal peer to Android and iOS. We hope to similarly continue our work in making Flutter the best way to create macOS desktop applications.

Our goal for this year is that you should be able to run `flutter create; flutter run` and have your application run on Web browsers, macOS, Android, Fuchsia, and iOS, with support for hot reload, plugins, testing, and release mode builds. We intend to ensure that our Material Design widget library works well on all these platforms.

_We do not intend to focus on other platforms in 2020, such as Windows. We also don't intend to provide desktop-equivalents of the Cupertino widget library this year._

### Quality 

Our other main goal is to improve Flutter's quality, fixing bugs and addressing a few of the most-highly requested features. This covers a wide range of areas but we have a particular focus on our Cupertino library and iOS fidelity, our support for the long tail of Android devices, and the development experience.

We intend to deliver on long-anticipated features such as our router refactor, instance state saving and restoring, and an improved internationalization workflow.

_We mainly use the "Thumbs-Up" emoji reactions on the first comment of an issue to determine it's importance. See the [Issue hygiene](https://github.com/flutter/flutter/wiki/Issue-hygiene) wiki page for more details on our prioritization strategy._

## Release Channels and Cadence

Flutter developers can select a “channel” from which to receive updates. We have four channels: master, dev, beta and stable, with increasing levels of stability and confidence of quality but longer lead time for changes to propagate. 

We release builds to the dev channel every few days, based on performing some additional testing. We plan to release one beta build each month, typically near the start of the month, and about four stable releases throughout the year. We recommend that you use the stable channel for apps released to end-users. For more details on our release process, see the [Release process](https://github.com/flutter/flutter/wiki/Release-process) wiki page.

## Milestones & Dates

If you’re interested in observing what we’ve planned out on a monthly basis, you can look through our [milestones](https://github.com/flutter/flutter/milestones?direction=asc&sort=due_date&state=open) page on GitHub. Having said that, be aware that issues often change milestones and none of the milestones should be viewed as committed dates.

We're also tracking a number of time-specific requirements imposed by platform vendors; see [here](https://github.com/flutter/flutter/wiki/Flutter-Critical-Requirement-Dates) for a list of issues.