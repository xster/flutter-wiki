## Flutter's channels

Flutter has the following channels, in increasing order of stability:

### master

The current tip-of-tree, absolute latest cutting edge build. Usually functional, though sometimes we accidentally break things.

### dev

The latest fully-tested build. Usually functional, but see [[Bad Builds]] for a list of known "bad" dev builds. We continually try to roll `master` to `dev`. Doing so involves running many more tests than those that we run during `master` development, which is why this is not actually the same to `master`.

### beta

Every month, we pick the "best" `dev` build of the previous month or so, and promote it to `beta`. These builds have been tested with our [[codelabs]].

### stable

When we believe we have a particularly good build, we promote it to the `stable` channel. We intend to do this more or less every quarter, but this may vary. We recommend that you use this channel for all production app releases. We may ship hotfixes to the stable channel for high-priority bugs, although our intent is to do this rarely.

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

We would recommend using the `stable` branch.

That said, the `beta` branch should be fine. There is no extra level of testing that we do for `stable` than for `beta`, other than time on the `beta` branch. So if there is something you want to use that is available on `beta` but not `stable`, feel free to consider using `beta`.

Using `dev` is a little less safe; if you use a `dev` build then we recommend watching the [[Bad Builds]] page to see if known ship-blocking bugs get reported for that branch. (The `dev` builds are our release candidates.) It is reasonable, however, to pick a `dev` build, test heavily with that build, and then keep using that build. That's what we're actually doing when picking a `dev` build to put on the `beta` branch: the only additional testing that we actually do to `beta` builds over `dev` builds is checking for basic things like "do our codelabs still work with this build"; beyond that, we just monitor bug reports. Most of our testing is done as pre-commit tests and tests run as part of releasing a `dev` build, so by the time we release a `dev` build, we have relatively high confidence that there isn't a serious problem.

## Will a particular bug fix be provided in a hotfix release?

Almost certainly not. We only hotfix the most egregious bugs (e.g. "nobody can build on iOS any more"), and even then we're more likely to just release a new build.

If you really need a particular patch and it's a fix to the flutter/flutter repository, you should feel free to create a Flutter branch yourself on your development machine and cherry-pick the fix you want onto that branch. Flutter is distributed as a `git` repository and all of `git`'s tools are available to you. If you need a particular patch that's from the flutter/engine repository or one of our dependencies (e.g. Dart or Skia), you could build your own engine but it's probably easier to just wait until the next release. On average, the next `dev` release is about a day away and the next `beta` release is about two weeks away.

## See also

* [[Release process]], which describes the details for how we push builds from channel to channel.
* [[Changelog]], where we describe changes since the last beta release.