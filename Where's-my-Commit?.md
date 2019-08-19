## Overview

Frequently, issues will be fixed in repositories other than `flutter/flutter`.  When this happens, it's common to want to know when the fix will be available in the framework.

Other times, issues will be root-caused to a particular commit, and it's common to want to know which Flutter versions are afflicted by the issue.

This page outlines the current process for determining that information.

## Process

### Finding the versions that contain Framework commit X

To find the published Flutter versions that contain any given commit in `flutter/flutter`:

1. Navigate to the page for that commit.  You can do this by either clicking on the commit from https://github.com/flutter/flutter/commits, or by constructing the URL manually.  The URL format is `https://github.com/flutter/flutter/commit/<sha>`.

1. Look in the lower left corner of the commit description box for a list of version tags.  For example:

   <img src="https://user-images.githubusercontent.com/15253456/63279347-ace5d000-c25d-11e9-89a6-f2bd37359e8e.png" alt="Versions" width="65%;" />

   If there are no versions listed there, it means that this commit has not yet been published to the dev channel and exists only on master.

### Finding the Framework commit that contains Engine commit X

To find out when a given engine commit rolled into the framework:

1. Find the engine PR that corresponds to your desired commit.  For example, for commit [7292d47](https://github.com/flutter/engine/commit/7292d47e615aff38180c8a4c1e65091325b6aae7), the corresponding PR is [flutter/engine#11206](https://github.com/flutter/engine/pull/11206).

   If the commit in question does not have a corresponding PR (most commonly seen with Dart rolls), then use the newest commit _before_ your desired engine commit that has a corresponding PR as a proxy.

   Once you've identified an engine PR, navigate to that PR.

1. Somewhere in the comment stream for the PR (usually at or near the bottom), there will be a cross-referenced "_Roll engine &lt;commit&gt;..&lt;commit&gt;_" link with a "Merged" badge to the right of it.  For example:

   <img src="https://user-images.githubusercontent.com/15253456/63278598-6a6fc380-c25c-11e9-84e7-097bc0bc3fbe.png" alt="The commit at which it rolled into the framework" width="65%;" />

   That was the PR in which this engine commit merged into the framework.  Click on that link.

1. Near the bottom of the comment stream for the framework PR will be a line that says:

   "_&lt;user&gt; merged commit &lt;commit&gt;_"

   Usually, the user will be "_engine-flutter-autoroll_".  Click on the commit sha to navigate to the commit.

1. The full commit sha is contained in the URL.  It's also listed on the page:

   <img src="https://user-images.githubusercontent.com/15253456/63279082-39dc5980-c25d-11e9-9c95-bc6ed1fd5082.png" alt="The commit sha" width="65%;" />

1. To find the published Flutter versions that contain this commit, follow the process above.

### Finding the Engine commit that contains Dart SDK commit X

To find out when a given Dart SDK commit rolled into the engine:

1. ...

### Finding the Skia commit that contains Dart SDK commit X

To find out when a given Skia commit rolled into the engine:

1. ...
