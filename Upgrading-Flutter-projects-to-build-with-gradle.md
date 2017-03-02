## Introduction

Prior to [Pull Request 7902](https://github.com/flutter/flutter/pull/7902) -- which was merged to master on February 6th 2017 -- we used a custom build process when building for Android. We now build for Android using the same standard 'gradle-based' build process that Android Studio project uses. This makes it easier to edit the native Android code; after the upgrade you can open the `android` folder in Android Studio, and it will know how to build the project, and will allow you to add java code.

If you have a project that was created prior to this date, please follow these steps to switch to building with gradle. This is required as we will be removing the custom build support shortly.

## Upgrading an existing project

*Note*: These steps apply to projects created with `flutter create` prior to February 6th 2017. If your project was based on a copy of `/examples/hello_services/`, then you just need to synchronize the contents of your `build.gradle` files from steps 4. and 5.

### Step 1: Move `AndroidManifest.xml`

```
cd <root dir of your project>
mkdir -p android/app/src/main
mv android/AndroidManifest.xml android/app/src/main/
```

### Step 3: Add `gradle.properties`

Create a new file `gradle.properties` in `<app root dir>/android/` with the following contents:

```
org.gradle.jvmargs=-Xmx1536M
```

### Step 4: Add `build.gradle` for the project

Create a new file `build.gradle` in `<app root dir>/android/` with the following contents:

```
buildscript {
    repositories {
        jcenter()
    }

    dependencies {
        classpath 'com.android.tools.build:gradle:2.2.3'
    }
}

allprojects {
    repositories {
        jcenter()
    }
}

task clean(type: Delete) {
    delete rootProject.buildDir
}

task wrapper(type: Wrapper) {
    gradleVersion = '2.14.1'
}
```


### Step 5: Add `build.gradle` for the main module

Create a new file `build.gradle` in `<app root dir>/android/app/` with the following contents:

```
def localProperties = new Properties()
def localPropertiesFile = rootProject.file('local.properties')
if (localPropertiesFile.exists()) {
    localPropertiesFile.withInputStream { stream ->
        localProperties.load(stream)
    }
}

def flutterRoot = localProperties.getProperty('flutter.sdk')
if (flutterRoot == null) {
    throw new GradleException("Flutter SDK not found. Define location with flutter.sdk in the local.properties file.")
}

apply plugin: 'com.android.application'
apply from: "$flutterRoot/packages/flutter_tools/gradle/flutter.gradle"

android {
    compileSdkVersion 25
    buildToolsVersion '25.0.2'

    lintOptions {
        disable 'InvalidPackage'
    }

    defaultConfig {
        testInstrumentationRunner "android.support.test.runner.AndroidJUnitRunner"
    }

    buildTypes {
        release {
            // TODO: Add your own signing config for the release build.
            // Signing with the debug keys for now, so `flutter run --release` works.
            signingConfig signingConfigs.debug
        }
    }
}

flutter {
    source '../..'
}

dependencies {
    androidTestCompile 'com.android.support:support-annotations:25.0.0'
    androidTestCompile 'com.android.support.test:runner:0.5'
    androidTestCompile 'com.android.support.test:rules:0.5'
}
```

### Step 6: Move icon resources

This step is only required if your app has custom launcher icons.

```
cd <root dir of your project>
mv android/res android/app/src/main/
```

### Notes

For a concrete example of this, see [this commit](https://github.com/flutter/flutter/pull/8173/commits/3a7507f80bfec22e36664b766a8730dcff2f80ec) that performed this upgrade on the `/examples/flutter_gallery/` project.

## Working with the project after upgrading

After upgrading you can build with `flutter build apk` and run with `flutter run`. The first build will take a little longer (as it will download and then cache a few gradle files).

In addition, you should now be able to edit and build from Android Studio:

1. Start Android Studio
1. Invoke File > Open...
1. Select the `android` folder inside your main app folder, and press OK
1. Press the Run button, or use the Run > Run menu item to build and run the app