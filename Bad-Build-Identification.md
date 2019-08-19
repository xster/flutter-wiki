#### What makes for a bad build?

Bad builds are [tagged dev releases](https://github.com/flutter/flutter/releases) that are affected by issues serious enough to keep us from releasing the build to a more stable channel (e.g. beta).

#### I found such an issue; how do I know which tagged dev releases are affected?

First, you need to identify which commit introduced the issue and which commit fixed the issue.  Once you've done that, you can see which version tags apply to any given commit by viewing the commit in GitHub:

![Bad commit](https://i.imgur.com/mWRTcaE.png)

In this case, the breaking change affects builds as far back as `v0.1.5`.  Once the fix commit has landed, you'll know which build picked up the fix.

More detailed information can be found in the [Where's my Commit?](https://github.com/flutter/flutter/wiki/Where's-my-Commit%3F) wiki page.

#### What do I do once I know which dev releases are affected?

You update the [[Bad Builds]] page accordingly.