If you’re installing or using Flutter in China, it may be helpful to use a trustworthy local mirror site that hosts Flutter’s dependencies. To instruct the Flutter tool to use an alternate storage location, you will need to set two environment variables, `PUB_HOSTED_URL` and `FLUTTER_STORAGE_BASE_URL`, before running the `flutter` command. 

Taking MacOS or Linux as an example, here are the first few steps in the setup process for using a mirror site.  Run the following in a Bash shell from the directory where you wish to store your local Flutter clone:

```bash
export PUB_HOSTED_URL=https://pub.flutter-io.cn
export FLUTTER_STORAGE_BASE_URL=https://storage.flutter-io.cn
git clone https://github.com/flutter/flutter.git
(cd ./flutter && git checkout alpha)
export PATH="$PWD/flutter/bin:$PATH"
```

After these steps, you should be able to [run flutter doctor](https://flutter.io/setup-macos/#run-flutter-doctor), and continue [setting up Flutter](https://flutter.io/setup/) normally.  From here on, packages fetched by `flutter packages get` will be downloaded from `flutter-io.cn` in any shell where `PUB_HOSTED_URL` and `FLUTTER_STORAGE_BASE_URL` are set.

The `flutter-io.cn` server is a provisional mirror for Flutter dependencies and packages maintained by [GDG China](http://www.chinagdg.com/). The Flutter team cannot guarantee long-term availability of this service. You’re free to use other mirrors if they become available. If you’re interested in setting up your own mirror in China, please contact flutter-dev@googlegroups.com for assistance. 