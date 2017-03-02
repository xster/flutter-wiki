## Introduction

Prior to [Pull Request 7902](https://github.com/flutter/flutter/pull/7902) -- which was merged to master on February 6th 2017 -- we used a custom build process when building for Android. We now build for Android using the same standard 'gradle-based' build process that Android Studio project uses. This makes it easier to edit the native Android code; after the upgrade you can open the `android` folder in Android Studio, and it will know how to build the project, and will allow you to add java code.

If you have a project that was created prior to this date, please follow these steps to switch to building with gradle. This is required as we will be removing the custom build support shortly.

*Note*: These steps apply to projects created with `flutter create` prior to February 6th 2017. If your project was based on a copy of `/examples/hello_services/`, then you just need to synchronize the contents of your `build.gradle` files from steps 4. and 5.

## Upgrading an existing project

The steps below use `<existing-app-dir>` as a placeholder for the directory containing your existing app, e.g. `~/dev/flutter/awesomeapp`.

### Step 1: Create a new project with the new template

```
flutter create <appname>
mv <appname>/lib <appname>/lib.old
```

### Step 2: Merge over your Dart code

```
cp <existing-app-dir>/lib <appname>/
```

### Step 3: Synchronize AndroidManifest.xml

This step is only required if you made any changes to `<existing-app-dir>android/AndroidManifest.xml`.

If that is the case, apply those changes to the new manifest, `<appname>/android/app/src/main/AndroidManifest.xml` 

### Step 4: Synchronize pubspec.yaml

**Note:** This step is only required if you made any changes to `<existing-app-dir>/pubspec.yaml`.

If that is the case, apply those changes to the new manifest, `<appname>/pubspec.yaml` 

### Step 5: Move icon resources, and other resources as applicable

**Note:** This step is only required if your app has custom launcher icons.

```
cp <existing-app-dir>/android/res <appname>android/app/src/main/
```

Repeat this for any other resources you may have.

### Step 6: Move over iOS code if applicable

**Note:** This step is only required if your app has any custom iOS code.

```
mv <appname>/iOS <appname>/iOS.old
cp <existing-app-dir>/iOS/ <appname>
```

## Working with the project after upgrading

When you are done, the structure of your `android` folder should match that of a new flutter projects created with `flutter create` using a recent framework.

You can now build with `flutter build apk` and run with `flutter run`. The first build will take a little longer (as it will download and then cache a few gradle files).

In addition, you can edit and build from Android Studio:

1. Start Android Studio
1. Invoke File > Open...
1. Select the `android` folder inside your main app folder, and press OK
1. Press the Run button, or use the Run > Run menu item to build and run the app