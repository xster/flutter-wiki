Flutter has the following channels (as selected by `flutter channel`):

* `master`: The current tip-of-tree, absolute latest cutting edge build. Usually functional, though sometimes we accidentally break things.

* `dev` (previously known as `alpha`): The latest fully-tested build. Usually functional, but see [[Bad Builds]] for a list of known "bad" dev builds.

* `beta`: Every few weeks, we pick the "best" `dev` build of the previous month or so, and promote it to `beta`. These builds have been tested with our [[codelabs]].

Eventually we will also have a `release` build, which will be the best `beta` build of the previous year or so, but we haven't yet reached a quality level that we are sufficiently happy with to use this label yet.