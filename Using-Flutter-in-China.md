_(This page will be deleted on November 5th 2018; see flutter.io for more recent information. When deleting this page, update `bin/internal/update_dart_sdk.sh` as well.)_

There's now a localized website for Flutter in China, here: https://flutter-io.cn. 

If you’d like to install Flutter using [an installation bundle](https://flutter.io/sdk-archive/), you can replace the domain of the original URL with a trusted mirror to speed it up. For example:
* Original URL: https://storage.googleapis.com/flutter_infra/releases/beta/windows/flutter_windows_v0.3.2-beta.zip
* Mirrored URL: https://storage.flutter-io.cn/flutter_infra/releases/beta/windows/flutter_windows_v0.3.2-beta.zip

You will still need to set up two environment variables to upgrade Flutter and use the pub package repository in China. Please follow the instructions below.

----

If you’re installing or using Flutter in China, it may be helpful to use a trustworthy local mirror site that hosts Flutter’s dependencies. To instruct the Flutter tool to use an alternate storage location, you will need to set two environment variables, `PUB_HOSTED_URL` and `FLUTTER_STORAGE_BASE_URL`, before running the `flutter` command. 

Taking MacOS or Linux as an example, here are the first few steps in the setup process for using a mirror site.  Run the following in a Bash shell from the directory where you wish to store your local Flutter clone:

```bash
export PUB_HOSTED_URL=https://pub.flutter-io.cn
export FLUTTER_STORAGE_BASE_URL=https://storage.flutter-io.cn
git clone -b dev https://github.com/flutter/flutter.git
export PATH="$PWD/flutter/bin:$PATH"
cd ./flutter
flutter doctor
```

After these steps, you should be able to continue [setting up Flutter](https://flutter.io/setup/) normally.  From here on, packages fetched by `flutter packages get` will be downloaded from `flutter-io.cn` in any shell where `PUB_HOSTED_URL` and `FLUTTER_STORAGE_BASE_URL` are set.

The `flutter-io.cn` server is a provisional mirror for Flutter dependencies and packages maintained by [GDG China](http://www.chinagdg.com/). The Flutter team cannot guarantee long-term availability of this service. You’re free to use other mirrors if they become available. If you’re interested in setting up your own mirror in China, please contact flutter-dev@googlegroups.com for assistance. 

Known issue:
* Running the Flutter Gallery app from source requires assets hosted on a domain this workaround currently doesn't support. You can subscribe to [this bug](https://github.com/flutter/flutter/issues/13763) to receive updates. In the meantime, you can check out Flutter Gallery from Google Play or third-party app stores you trust.

***
Other community-run mirror sites:
* Shanghai Jiaotong University Linux User Group
  * FLUTTER_STORAGE_BASE_URL: https://mirrors.sjtug.sjtu.edu.cn/
  * PUB_HOSTED_URL: https://dart-pub.mirrors.sjtug.sjtu.edu.cn/

Please use these community mirror sites only if you _trust_ the provider. The Flutter team cannot verify their reliability nor security.