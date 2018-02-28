### What makes for a bad build?

Bad builds are [tagged dev releases](https://github.com/flutter/flutter/releases) that are affected by issues serious enough to keep us from releasing the build to a more stable channel (e.g. beta).

FYI, if you see a GitHub issue cross your path that you believe should keep affected builds from being released to a more stable channel (e.g. beta), please update the https://github.com/flutter/flutter/wiki/Bad-Builds page accordingly.  The release process calls for us to consult the list of bad builds when we decide which release gets promoted to the beta branch (and eventually the release branch), so keeping the list of bad builds up-to-date is important.

### I found such an issue; how do I know which tagged dev releases are affected?

First, you need to identify which commit introduced the issue and which commit fixed the issue.  Once you've done that, you can see which version tags apply to any given commit by viewing the commit in GitHub:

![Bad commit](https://i.imgur.com/mWRTcaE.png)

In this case, the breaking change affects builds as far back as `v0.1.5`.  Once the fix commit has landed, you'll know which build picked up the fix.

### What do I do once I know which dev releases are affected?

You update the [[Bad Builds]] page accordingly.