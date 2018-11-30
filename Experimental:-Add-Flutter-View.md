_**Everything in this doc and linked from this doc is experimental. These details WILL change. Do not use these instructions or APIs in production code because we will break you.**_

# Add a Flutter View

Many Android apps cannot use `FlutterActivity`s because they navigate to multiple screens within a single `Activity`. Additionally, some apps prefer not to use `Fragment`s, which precludes the use of `FlutterFragment`. In a situation where only a `View` will do, Flutter offers `FlutterView`.

`FlutterView` requires a lot more call-forwarding than a `FlutterFragment` or `FlutterActivity`, so if you can use a `FlutterFragment` or `FlutterActivity`, you should consider using one of those instead so that you have less code to maintain.

* [How to use a `FlutterActivity`](https://github.com/flutter/flutter/wiki/Experimental:-Add-Flutter-Activity)
* [How to use a `FlutterFragment`](https://github.com/flutter/flutter/wiki/Experimental:-Add-Flutter-Fragment)

**TODO(mattcarroll):** fill in rest of this document