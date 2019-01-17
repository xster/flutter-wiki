## Flutter's channels

Flutter has the following channels, in increasing order of stability:

### master

The current tip-of-tree, absolute latest cutting edge build. Usually functional, though sometimes we accidentally break things.

### dev

The latest fully-tested build. Usually functional, but see [[Bad Builds]] for a list of known "bad" dev builds. We continually try to roll `master` to `dev`. Doing so involves running many more tests than those that we run during `master` development, which is why this is not actually the same to `master`.

### beta

Every month, we pick the "best" `dev` build of the previous month or so, and promote it to `beta`. These builds have been tested with our [[codelabs]].

### stable

When we believe we have a particularly good build, we promote it to the `stable` channel. This has so far happened only once, with our 1.0 launch. We intend to do this more or less every quarter, but this may vary.

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

## See also

* [[Release process]], which describes the details for how we push builds from channel to channel.
* [[Changelog]], where we describe changes since the last beta release.