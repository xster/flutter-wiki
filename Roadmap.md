# 2019

With the [release of Flutter 1.0](https://developers.googleblog.com/2018/12/flutter-10-googles-portable-ui-toolkit.html), we’ve made a good start but we’ve still got lots of work to do!

In the interest of transparency, we want to share high-level details of our roadmap, so that others can see our priorities and make plans based off the work we are doing.

We’ve established some broad themes that we want to focus on over the coming year:

- Fundamentals
- Ease of adoption
- Ecosystem
- Support for platforms beyond mobile
- Dynamic updates
- Tooling

Our plans will of course evolve over time based on customer feedback and new market opportunities. The list here shouldn't be viewed either as exhaustive, nor a promise that we will complete all this work. If you have feedback about what you think we should be working on, we encourage you to get in touch (e.g. by [filing an issue](https://github.com/flutter/flutter/issues/new/choose) or [emailing the flutter-dev mailing list](https://groups.google.com/forum/#!forum/flutter-dev)). And as Flutter is an open source project, we invite contributions both towards the themes presented below and in other areas.

## Release Channels and Cadence

Flutter developers can select a “channel” from which to receive updates. We have four channels: master, dev, beta and stable, with increasing levels of stability and confidence of quality but longer lead time for changes to propagate. 

We plan to release one beta build each month, typically near the start of the month, and about four stable releases throughout the year. We recommend that you use the stable channel for apps released to end-users. For more details on our release process, see the [Release process](https://github.com/flutter/flutter/wiki/Release-process) wiki page.

## Themes

### Fundamentals

Our first goal is to continue to add polish to Flutter’s existing foundation:

- **Bug fixing**, mainly based on how many "Thumbs-Up" emoji reactions issues have (see the [Issue hygiene](https://github.com/flutter/flutter/wiki/Issue-hygiene) and [Issue triage](https://github.com/flutter/flutter/wiki/Triage) wiki pages for more details on our prioritization strategy). (New features, except as specifically called out below, are an area we are intentionally downplaying as we first fix bugs in the features we have already implemented before adding more features.)
- **Performance**, including memory usage, engine footprint, frame times, and so forth. If you have specific benchmarks you would like us to work on, providing them as [devicelab tests](https://github.com/flutter/flutter/tree/master/dev/devicelab) is the best way to get us to look at them!
- **Improving testing** to ensure we are confident that we can reliably and quickly provide stable builds without regressions.
- **Error message improvement**, based on user research, to make errors more actionable and include common solutions.
- **API documentation improvements**, including in particular providing sample code and diagrams, to continue to make our APIs easy to use.

### Ease of Adoption

We want to continue to reduce friction for new Flutter developers to get started with their Flutter journey, for example:

- Addressing the needs of developers who want to **[add Flutter to existing applications](https://github.com/flutter/flutter/wiki/Add-Flutter-to-existing-apps)**, for example providing a new module template and a new Android embedding API.
- Updating and expanding the **documentation on Flutter’s Web site** with more high-level architectural documentation and more high-level cookbook-style documentation.
- Addressing **best practices for managing state** in a Flutter application.
- Continuing our investment in the **Cupertino widget library**, to help authors who are specifically targeting iOS.
- Making it easy to **try Flutter without installing** the full tooling and runtime stack.

### Ecosystem

Ecosystem in Flutter means making it easy for Flutter users to do everything they need to quickly, even if the core Flutter framework doesn’t come with out-of-the-box support. Towards that end, we spend a lot of our time on tooling and infrastructure to enable a growing ecosystem around the core Flutter technologies. Google will also spend some time contributing plugins and tooling that you expect from Google.

Specific ecosystem work that we’re planning in 2019:

- Better support for using C/C++ libraries from Flutter, including **direct calls to C/C++ from Dart and vice versa**.
- Bring **Flutter’s first-party [plugins](https://github.com/flutter/plugins/tree/master/packages) and [packages](https://github.com/flutter/packages/tree/master/packages)** up to the same quality and completeness level as the core framework.
- **Complete work on the Maps and WebView plugins** for iOS and Android.
- Make sure that Flutter applications can **take advantage of Google services**, including in-app payments and YouTube.
- Provide support for  **local notifications** and **local data storage**.

### Support for platforms beyond mobile

We will invest in expanding Flutter to other form factors and release profiles, in pursuit of our goal to build a portable UI toolkit for any place where pixels are painted. 

- Improved support for **keyboard and mouse input**.
- Making [the **Hummingbird** project](https://medium.com/flutter-io/hummingbird-building-flutter-for-the-web-e687c2a023a8) (Flutter running on the Web) available to developers.
- Continue to experiment with **making Flutter run on desktop-class platforms** (e.g. macOS and Windows).

### Dynamic updates

The Dart Platform, on which Flutter is built, provides unique abilities for us to push code to your applications without redeploying the app.

- **Dynamic patching on Android**, allowing for code updates to deployed to Flutter applications running on Android directly from a servers.
- **Dynamic extension loading** to allow lazy loading of occasionally used parts of your application.

### Tooling

- Continue to invest in **Visual Studio Code, Android Studio, and IntelliJ** as first-class IDEs.
- Add support for **[Language Server Protocol](https://langserver.org/)**, and other open protocols, to our tools.
- Make it **easy for developers to improve the quality and performance of their applications** with improved profiling and debugging experiences.
- Continue to **improve templates** to make getting started with Flutter work quick and easy.

## Milestones & Dates

If you’re interested in observing what we’ve planned out on a monthly basis, you can look through our [milestones](https://github.com/flutter/flutter/milestones?direction=asc&sort=due_date&state=open) page on GitHub. Having said that, be aware that issues often change milestones and none of the milestones should be viewed as committed dates.