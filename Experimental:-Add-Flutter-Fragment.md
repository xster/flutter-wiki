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

```java
  private void createFlutterFragment() {
    FragmentManager fragmentManager = getFragmentManager();

    flutterFragment = (FlutterFragment) fragmentManager.findFragmentByTag(TAG_FLUTTER_FRAGMENT);

    if (flutterFragment == null) {
      // No FlutterFragment exists yet. This must be the initial Activity creation. We will create
      // and add a new FlutterFragment to this Activity.
      //
      // If isSplashScreenDesired() is true, Flutter makes itself transparent until the 1st frame is rendered
      // so that Flutter avoids displaying an initial black screen.
      //
      // The "initial route" is the String that corresponds to the 1st route that the given Flutter app
      // will display. The default value is "/".
      //
      // The "app bundle path" refers to filesystem asset location of the source code for your Flutter app.
      // If you do not have any reason to customize the "app bundle path" then use:
      // "FlutterMain.findAppBundlePath(getApplicationContext())"
      flutterFragment = FlutterFragment.newInstance(
          isSplashScreenDesired(),
          getInitialRoute(),
          getAppBundlePath()
      );

      // This method assumes that there is a FrameLayout in the view hierarchy with an ID of CONTAINER_ID.
      // TAG_FLUTTER_FRAGMENT is a String of your choice to reference the added FlutterFragment.
      fragmentManager
          .beginTransaction()
          .add(CONTAINER_ID, flutterFragment, TAG_FLUTTER_FRAGMENT)
          .commit();
    }
  }
```

## Use a FlutterFragment in an Activity

Whether a developer chooses to inflate a `FlutterFragment` from XML, or instantiate a `FlutterFragment` in code, the usage of the `FlutterFragment` is the same.

```java
public class MyActivity extends FragmentActivity {

  private FlutterFragment flutterFragment;

  @Override
  protected void onCreate(Bundle savedInstanceState) {
    super.onCreate(savedInstanceState);
    setContentView(R.layout.my_xml_layout);

    flutterFragment = // Either inflate from XML, or instantiate programmatically.
  }

  @Override
  public void onPostResume() {
    super.onPostResume();
    // Forward onPostResume() to the FlutterFragment so that plugins receive the callback.
    flutterFragment.onPostResume();
  }

  @Override
  protected void onNewIntent(Intent intent) {
    // Forward Intents to our FlutterFragment in case it cares.
    flutterFragment.onNewIntent(intent);
  }

  @Override
  public void onBackPressed() {
    // Forward onBackPressed() to the FlutterFragment so that the Flutter UI has an opportunity to respond.
    flutterFragment.onBackPressed();
  }

  @Override
  public void onRequestPermissionsResult(int requestCode, @NonNull String[] permissions, @NonNull int[] grantResults) {
    // Forward onRequestPermissionsResult() to the FlutterFragment in case any plugins need this information.
    flutterFragment.onRequestPermissionsResult(requestCode, permissions, grantResults);
  }

  @Override
  public void onUserLeaveHint() {
    // Forward onUserLeaveHint() to the FlutterFragment so that the Flutter UI has an opportunity to respond.
    flutterFragment.onUserLeaveHint();
  }
}
```

As shown above, some `Activity` callbacks need to be forwarded to the `FlutterFragment` to achieve correct Flutter behavior. These callbacks are only available in `Activity`s, which is why the callbacks need to be forwarded.