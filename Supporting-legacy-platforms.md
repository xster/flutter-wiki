## Building ARMv7 (iOS) & armeabi v7a (Android) with Xcode10

In Xcode10, the i386 architecture is deprecated for macOS, so building the Flutter engine for armv7/armeabi-v7a fails. Specifically, libraries like CoreFoundation contain only code for the x86_64 architecture.

![iOS ARMv7](https://user-images.githubusercontent.com/817851/45751101-e7a54980-bc43-11e8-833f-b6458c9a4762.png)

![Android armeabi-v7a](https://user-images.githubusercontent.com/817851/45751099-e70cb300-bc43-11e8-97fa-a877dff5449d.png)

To address this, get the MacOS 10.13 SDK from Xcode 9.x from [Apple](https://developer.apple.com/download/more/), and extract the SDK components from the `.xip` file. Uncompress the SDK into `/Applications/Xcode.app/Contents/Developer/Platforms/MacOSX.platform/Developer/SDKs` and name the SDK `MacOSX10.13.sdk`:

![Uncompressed SDK in Xcode10](https://user-images.githubusercontent.com/817851/45752211-47512400-bc47-11e8-88fe-b738ac53831f.png)

To check if the logic is fine, run command below:

```bash
python your-flutter-engine-path/engine/src/build/mac/find_sdk.py 10.12
```

When `find_sdk.py` return 10.13, the ninja build will succeed for gen_snapshot (i386), Flutter.framework (ARMv7) and libflutter.so (armeabi-v7a).

## Build Flutter engine for 32bit iOS simulator on modern Mac(x86_64)

To build the Flutter engine for iOS simulator on a modern Mac(x86_64), the gn command will generate a `target_cpu` value with x64. Henceforth, the Flutter.framework and gen_snapshot will be x86_64. 
However, sometimes you may want to develop Flutter on a 32bit simulator(like iPhone5), you will need both Flutter.framework and gen_snapshot to be i386.

Follow instruction below to change the default behavior in gn command:
1. Edit your-flutter-engine-path/engine/src/flutter/tools/gn
![Edit gn](https://user-images.githubusercontent.com/817851/49006557-57840300-f1a4-11e8-850a-d019dc854bbd.png)
    
## Compiling `libflutter.so` for armeabi

When you build the engine for Android, the `--android-cpu=arm` targets `armeabi-v7a` (ARMv7), not `armeabi` (ARMv6). 

Follow instructions below to compile `libflutter.so` for armeabi

1. Edit `$ENGINE_HOME/src/build/config/arm.gni`

   ![Edit arm.gni](https://user-images.githubusercontent.com/817851/46179898-1c726a00-c2f0-11e8-9d32-9e9622cfc1cf.png)

   This will result in an armeabi build configuration when specified arm for android

2. Edit `$ENGINE_HOME/src/build/config/compiler/BUILD.gn`

   ![Edit BUILD.gn](https://user-images.githubusercontent.com/817851/45918117-7c15e300-beb4-11e8-9326-c5f9a148f0f0.png)

   This modification will solve the undefined symbol error as below:

   ![Link error when atomic library not linked](https://user-images.githubusercontent.com/817851/45918144-ff373900-beb4-11e8-96e2-68c08e54af8c.png)

3. Edit `$ENGINE_HOME/src/third_party/dart/runtime/vm/signal_handler.h`

   See also
[issue 22172 (non-ASM statement in naked function is not supported when building for armeabi)](https://github.com/flutter/flutter/issues/22172).

   Consider applying the [non-ASM statement patch](https://dart-review.googlesource.com/c/sdk/+/76021/3/runtime/vm/signal_handler.h).

4. Edit `$ENGINE_HOME/src/third_party/skia/BUILD.gn`

   ![Remove neon related code](https://user-images.githubusercontent.com/817851/46179988-72471200-c2f0-11e8-85eb-ff9eafeb7930.png)

   Visit [NEON support works with the armeabi-v7a and arm64-v8a](https://developer.android.com/ndk/guides/cpu-arm-neon) for more details.

5. Edit `$ENGINE_HOME/src/third_party/skia/gn/BUILDCONFIG.gn`

   ![armeabi instead of armeabi-v7a](https://user-images.githubusercontent.com/817851/46180048-b3d7bd00-c2f0-11e8-94a6-4861f8a3c3ee.png)

6. Edit `$ENGINE_HOME/src/third_party/android_tools/ndk`

   As NDK (v17 and above) does not provide the armeabi library, download NDK 16.1.4479499 and replace the one used by default for the Flutter engine. Please make sure the armeabi libraries exists as below:
    
   ![armeabi libraries](https://user-images.githubusercontent.com/817851/45918410-25f76e80-beb9-11e8-8b9c-bd8730c94cb5.png)