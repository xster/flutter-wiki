_**Everything in this doc and linked from this doc is experimental. These details WILL change. Do not use these instructions or APIs in production code because we will break you.**_

# Add Flutter as a Fragment in a ViewPager

Tabbed navigation often expects the presence of a `ViewPager`, such that the user can swipe left/right to navigate between tabbed pages. This guide shows you how to integrate Flutter as one or more of the pages in your `ViewPager`.

Start by implementing standard tabbed navigation in Android with a `ViewPager`. Consider following [this guide](https://developer.android.com/training/implementing-navigation/lateral).

Next, alter your `FragmentPagerAdapter` to return a `FlutterFragment` for the desired page(s).

```java
  /**
   * A {@link FragmentPagerAdapter} that returns a fragment corresponding to
   * one of the sections/tabs/pages.
   */
  public class SectionsPagerAdapter extends FragmentPagerAdapter {

    public SectionsPagerAdapter(FragmentManager fm) {
      super(fm);
    }

    @Override
    public Fragment getItem(int position) {
      if (position == FLUTTER_PAGE_INDEX) {
        // In this case we construct a FlutterFragment that will run
        // our main() method in Dart and start with an initial route of "/".
        return new FlutterFragment.Builder().build();
      } else {
        // return some other page's Fragment
      }
    }

    @Override
    public int getCount() {
      // You need to define PAGE_COUNT
      return PAGE_COUNT;
    }
  }
```

You should now have a Flutter UI as one or more pages within your tabbed navigation.

You may notice a delay between creation of your `FlutterFragment` and the display of your Flutter UI. This delay is caused by the warm-up time for the `FlutterEngine`. You may want to create your `FlutterFragment` ahead of time to avoid this warm-up, and cache it across `Activity` configuration changes. Or, you may want to create an instance of `FlutterEngine` ahead of time, cache it in a static reference, and then create a subclass of `FlutterFragment` that uses your static-cached `FlutterEngine` instead of automatically creating a new one.

TODO(mattcarroll): link to guide for subclassing `FlutterFragment` with a cached engine.