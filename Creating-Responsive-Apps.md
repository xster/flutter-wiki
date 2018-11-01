_(this page will be deleted once we move it to flutter.io -- see https://github.com/flutter/website/issues/1567)_

Flutter allows you to create apps that self-adapt to the device's screen size and orientation.

There are two basic approaches to creating Flutter apps with responsive design:

+ **Use the [`LayoutBuilder`](https://docs.flutter.io/flutter/widgets/LayoutBuilder-class.html) class:** From its  [`builder`](https://docs.flutter.io/flutter/widgets/LayoutBuilder/builder.html) property, you get a [`BoxConstraints`](https://docs.flutter.io/flutter/rendering/BoxConstraints-class.html). Examine the constraint's  properties to decide what to display. For example, if your [`maxWidth`](https://docs.flutter.io/flutter/rendering/BoxConstraints/maxWidth.html) is greater than your width breakpoint, return a [`Scaffold`](https://docs.flutter.io/flutter/material/Scaffold-class.html) object with a row that has a list on the left. If it's narrower, return a [`Scaffold`](https://docs.flutter.io/flutter/material/Scaffold-class.html) object with a drawer containing that list. You can also adjust your display based on the device's height, the aspect ratio, or some other property. When the constraints change (e.g. the user rotates the phone, or puts your app into a tile UI in Nougat), the build function will rerun.

+ **Use the [`MediaQuery.of()`](https://docs.flutter.io/flutter/widgets/MediaQuery/of.html) method in your `build` functions:** This gives you the size, orientation, etc, of your current app. This is more useful if you want to make decisions based on the complete context rather than on just the size of your particular widget. Again, if you use this, then your `build` function is automatically rerun if the user changes the app's size somehow.

These other widgets may also be useful if you want to dynamically adjust an app's display:

+ [`AspectRatio`](https://docs.flutter.io/flutter/widgets/AspectRatio-class.html) lets you force a child widget to a particular aspect ratio.
+ [`FittedBox`](https://docs.flutter.io/flutter/widgets/FittedBox-class.html) scales its child to fit.
+ [`CustomMultiChildLayout`](https://docs.flutter.io/flutter/widgets/CustomMultiChildLayout-class.html) lets you lay out your children based on the incoming size.