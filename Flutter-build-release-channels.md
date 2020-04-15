## Flutter's channels

Flutter has the following channels, in increasing order of stability:

### master

The current tip-of-tree, absolute latest cutting edge build. Usually functional, though sometimes we accidentally break things.

### dev

The latest fully-tested build. Usually functional, but see [[Bad Builds]] for a list of known "bad" dev builds. We continually try to roll `master` to `dev`. Doing so involves running many more tests than those that we run during `master` development, which is why this is not actually the same to `master`.

dev releases will be tagged with the following schema:

[x].[y].0-[n].[m].pre

If master is currently at x=1, y=18, n will increment by one for each subsequent beta release.  m will increment for each subsequent build at this branch point. 

Versioning example:
```
1.18.0-1.0.pre <- first dev build after master moves to 1.18
1.18.0-2.0.pre <- next dev build from a more recent point on master
1.18.0-2.1.pre <- point release from the same point of master as the above build
```
### beta

We will branch from master for a new beta release at the beginning of the month, usually the first Monday.  This will include a branch for Dart, the Engine and the Framework.  This branch will then be "stabilized" for the next couple of weeks, meaning we will accept [cherrypick](https://github.com/flutter/flutter/wiki/Flutter-Cherrypick-Process) requests for high impact issues.  As we near the end of the month and the next beta branch, we will likely reduce the number of cherrypicks we are willing to do.  Once a quarter, the beta branch will live on to become the next stable branch, as detailed below.

Versioning example:

following from the dev example above, let's say we branch for beta at the 15th dev release point for 1.18
```
1.18.0-15.0-pre <- initial beta RC, same release as went to dev.
1.18.0-15.1-pre <- subsequent build on the (now) beta branch with some cherrypicks.
1.18.0-15.2-pre <- second subsequent build.
```
### stable

Roughly once a quarter, a branch that has been stabilized on beta will become our next stable branch and we will create a stable release from that branch.  We recommend that you use this channel for all production app releases.  
In case of high severity, high impact or security issues, we may do a hotfix release for the stable channel.  This will follow the same [cherrypick](https://github.com/flutter/flutter/wiki/Flutter-Cherrypick-Process) process.

Versioning example:

the first stable release will always be X.Y.0.  following on the example above:
```
1.18.0-15.4-pre <- last beta build on branch
1.18.0 <- stable release
1.18.1 <- potential hotfix of 1.18.0
```
## How to change channels

You can see which channel you're on with the following command:

```
$ flutter channel
Flutter channels:
* stable
  beta
  dev
  master
```

To switch channels, run `flutter channel [<channel-name>]`, and then run `flutter upgrade` to ensure you're on the latest.

## Which channel should I use?

We recommend using the `stable` branch.

That said, the `beta` branch should be fine. There is no extra level of testing that we do for `stable` than for `beta`, other than the extended stabilization period on the `beta` branch. So if there is something you want to use that is available on `beta` but not `stable`, feel free to consider using `beta`.

Using `dev` is a little less safe; if you use a `dev` build then we recommend watching the [[Bad Builds]] page to see if known ship-blocking bugs get reported for that branch.  It is reasonable, however, to pick a `dev` build, test heavily with that build, and then keep using that build. That's what we're actually doing when picking a `dev` build to put on the `beta` branch: the only additional testing that we actually do to `beta` builds over `dev` builds is checking for basic things like "do our codelabs still work with this build"; beyond that, we just monitor bug reports. Most of our testing is done as pre-commit tests and tests run as part of releasing a `dev` build, so by the time we release a `dev` build, we have relatively high confidence that there isn't a serious problem.

## Will a particular bug fix be provided in a hotfix release?

Depending on the severity of the issue, it is possible.  Refer to the [cherrypick process](https://github.com/flutter/flutter/wiki/Flutter-Cherrypick-Process) for details.

If you really need a particular patch and it's a fix to the flutter/flutter repository, you should feel free to create a Flutter branch yourself on your development machine and cherry-pick the fix you want onto that branch. Flutter is distributed as a `git` repository and all of `git`'s tools are available to you. If you need a particular patch that's from the flutter/engine repository or one of our dependencies (e.g. Dart or Skia), you could build your own engine but it's probably easier to just wait until the next release. On average, the next `dev` release is about a day away and the next `beta` release is about two weeks away.

## See also

* [[Release process]], which describes the details for how we push builds from channel to channel.
* [[Changelog]], where we describe changes since the last beta release.
* [Cherrypick process](https://github.com/flutter/flutter/wiki/Flutter-Cherrypick-Process), where we cover how to request an issue for cherrypicking.