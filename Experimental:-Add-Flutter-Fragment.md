_**Everything in this doc and linked from this doc is experimental. These details WILL change. Do not use these instructions or APIs in production code because we will break you.**_

# Add a Flutter Fragment

Many Android apps achieve navigation by switching out different `Fragment`s as "screens" within a single `Activity`.  A `FlutterActivity` cannot be used in such apps because `Activity`s cannot be embedded within other `Activity`s.  To deal with this use-case, Flutter offers a `FlutterFragment` that can be used wherever any other `Fragment` can be used.

`FlutterFragment` provides most of the behavior that is provided by `FlutterActivity`.  In fact, `FlutterActivity` uses a `FlutterFragment` internally.

## Inflate a FlutterFragment from XML

One way to use a `FlutterFragment` is to declare it in an XML layout file.

```xml
<fragment
    android:id="@+id/flutterfragment"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    android:name="io.flutter.embedding.android.FlutterFragment"
    />
```

Reminder, declaring a `FlutterFragment`, or any other `Fragment`, within an XML layout file prevents the `Fragment` from being programmatically removed at runtime.

**TODO(mattcarroll):** Add support for XML attributes for splash screen, initial route, and possibly app bundle path. This work is currently blocked by the fact that the engine buildroot does not support AAR production, which prevents including any XML resources of any kind.

## Instantiate a FlutterFragment in code

Another way to use a `FlutterFragment` is to instantiate one within an `Activity`.

```java
  private void createFlutterFragment() {
    FragmentManager fragmentManager = getFragmentManager();

    flutterFragment = (FlutterFragment) fragmentManager.findFragmentByTag(TAG_FLUTTER_FRAGMENT);

    if (flutterFragment == null) {
      // No FlutterFragment exists yet. This must be the initial Activity creation. We will create
      // and add a new FlutterFragment to this Activity.
      //
      // This example uses defaults of "main" for the Dart entrypoint, and "/" as the initial route.
      flutterFragment = new FlutterFragment.Builder().build();

      // This method assumes that there is a FrameLayout in the view hierarchy with an ID of CONTAINER_ID.
      // TAG_FLUTTER_FRAGMENT is a String of your choice to reference the added FlutterFragment.
      fragmentManager
          .beginTransaction()
          .add(CONTAINER_ID, flutterFragment, TAG_FLUTTER_FRAGMENT)
          .commit();
    }
  }
```
