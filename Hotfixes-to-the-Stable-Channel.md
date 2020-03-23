In general, our philosophy is to update the `stable` channel on a quarterly basis with feature updates. In the intervening period, occasionally we may decide a bug or regression warrants a hotfix. We tend to be extremely conservative with these hotfixes, since there's always a risk that fixing one bug introduces a new one, and we want the `stable` channel to always represent our most tested builds. 

We intend to announce hotfixes to the [flutter-announce](https://groups.google.com/forum/#!forum/flutter-announce) group, and we recommend that you subscribe to that list if you publish an application using Flutter. 

Note that we only hotfix the latest version --- if you see bugs on older versions of the `stable` channel, please consider moving to the latest `stable` channel version. 

## Flutter 1.12 Changes
### [Hotfix.8](https://github.com/flutter/flutter/pull/50591) (February 11, 2020)
This fixes the following issues:
 - https://github.com/flutter/flutter/issues/50066 - binaries unsigned in last hotfix
 - https://github.com/flutter/flutter/issues/49787 - in a previous hotfix, we inadvertently broke Xcode 10 support. Reverting this change would have caused other problems (and users would still have to upgrade their Xcode with the next stable release), we decided to increase our minimum supported Xcode version. Please see the linked issue for more context on this decision.
 - https://github.com/flutter/flutter/issues/45732 & https://github.com/flutter/flutter/issues/47609, two related Android log reader fixes.

### [Hotfix.7](https://github.com/flutter/flutter/pull/49437) (January 26, 2020)
This includes the following fixes: 
- https://github.com/flutter/flutter/issues/47164 - blackscreen / crash on certain Huawei devices
- https://github.com/flutter/flutter/issues/47804 - Flutter engine crashes on some Android devices due to "Failed to setup Skia Gr context"
- https://github.com/flutter/flutter/issues/46172 - reportFullyDrawn causes crash on Android KitKat

### Hotfix.5 (December 11, 2019)
Initial stable release.