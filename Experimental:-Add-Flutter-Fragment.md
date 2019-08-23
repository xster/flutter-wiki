_**Everything in this doc and linked from this doc is experimental. These details WILL change. Do not use these instructions or APIs in production code because we will break you.**_

# Add a Flutter Fragment

Many Android apps achieve navigation by switching out different `Fragment`s as "screens" within a single `Activity`.  A `FlutterActivity` cannot be used in such apps because `Activity`s cannot be embedded within other `Activity`s.  To deal with this use-case, Flutter offers a `FlutterFragment` that can be used wherever any other `Fragment` can be used.

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
      flutterFragment = new FlutterFragment.createDefault();

      // This method assumes that there is a FrameLayout in the view hierarchy with an ID of CONTAINER_ID.
      // TAG_FLUTTER_FRAGMENT is a String of your choice to reference the added FlutterFragment.
      fragmentManager
          .beginTransaction()
          .add(CONTAINER_ID, flutterFragment, TAG_FLUTTER_FRAGMENT)
          .commit();
    }
  }
```

## The FlutterEngine within FlutterFragment

A `FlutterEngine` object is responsible for executing the Dart code in your Flutter app. By default, every `FlutterFragment` creates a new `FlutterEngine` internally to run the desired Flutter UI.

One downside to having a `FlutterFragment` internally create a new `FlutterEngine` is that there is a warm-up period before the `FlutterEngine` is able to render a UI. This results in a temporary blank screen. One way to solve this is to create and start a `FlutterEngine` before the `FlutterFragment` appears, and then instruct the `FlutterFragment` to use the warmed up `FlutterEngine`. There are 3 ways to accomplish this, which are outlined below.

### Warm-up a FlutterEngine

Regardless of which option you use to provide a `FlutterEngine` to a `FlutterFragment`, you must first instantiate and pre-warm a `FlutterEngine` before the user navigates to your `FlutterFragment`.

```java
// Instantiate your FlutterEngine.
FlutterEngine flutterEngine = new FlutterEngine(context);

// Pre-warm your FlutterEngine by starting Dart execution.
flutterEngine
  .getDartExecutor()
  .executeDartEntrypoint(
    DartEntrypoint.createDefault()
  );
```

### Option 1: Use FlutterEngineCache

After pre-warming a `FlutterEngine`, place it in the `FlutterEngineCache`:

```java
FlutterEngineCache
  .getInstance()
  .put("my_engine_id", flutterEngine);
```

The engine ID in the previous example can be any value you choose, so long as it does not collide with other cached engines that you previously placed in the `FlutterEngineCache`.

Then, configure your `FlutterFragment` to use your cached `FlutterEngine`:

```java
FlutterFragment flutterFragment = FlutterFragment
  .withCachedEngine("my_engine_id")
  .build();
```

Now, when your `FlutterFragment` is displayed, it will use your pre-warmed `FlutterEngine`, greatly reducing the time it takes to display the first frame.

### Option 2: Subclass FlutterFragment

Another way to use a pre-existing `FlutterEngine` within a `FlutterFragment` is to subclass `FlutterFragment`:

```java
public class MyFlutterFragment extends FlutterFragment {
  @Override
  @Nullable
  protected FlutterEngine provideFlutterEngine(@NonNull Context context) {
    // Maybe you chose to store your FlutterEngine in a static reference.
    return MyFlutterEngine.getFlutterEngine();

    // or maybe you chose to store your FlutterEngine in your Application class.
    return ((MyApp) context.getApplication).getFlutterEngine();
  }
}
```

### Option 3: Implement FlutterEngineProvider in your Activity

A third way to use a pre-warmed `FlutterEngine` is to implement the `FlutterEngineProvider` in the `Activity` that owns your `FlutterFragment`:

```java
public class MyActivity extends Activity implements FlutterEngineProvider {
  @Override
  @Nullable
  FlutterEngine provideFlutterEngine(@NonNull Context context) {
    // Maybe you chose to store your FlutterEngine in a static reference.
    return MyFlutterEngine.getFlutterEngine();

    // or maybe you chose to store your FlutterEngine in your Application class.
    return ((MyApp) context.getApplication).getFlutterEngine();
  }
}
``` 