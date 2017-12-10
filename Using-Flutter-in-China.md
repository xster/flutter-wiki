If you’re installing or using Flutter in China, it may be helpful to use a trustworthy local mirror site that hosts Flutter’s dependencies. To instruct the Flutter tool to use an alternate storage location, you will need to set two environment variables, PUB_HOSTED_URL and FLUTTER_STORAGE_BASE_URL, before running the `flutter` command. 

Taking macOS as an example, here are the first few steps in the setup process using a mirror site: 

```
$ export PUB_HOSTED_URL=https://pub.flutter-io.cn
$ export FLUTTER_STORAGE_BASE_URL=https://storage.flutter-io.cn
$ git clone https://github.com/flutter/flutter.git
$ export PATH=`pwd`/flutter/bin:$PATH
```
After these steps, you should be able to [run flutter doctor](https://flutter.io/setup-macos/#run-flutter-doctor). At the moment (12/09/2017), only the master branch knows how to handle these two environment variables. The next alpha roll will incorporate the changes to the more stable alpha branch.   

Flutter-io.cn is a provisional mirror for Flutter dependencies and packages maintained by [GDG China](http://www.chinagdg.com/). The Flutter team cannot guarantee long-term availability of this service. You’re free to use other mirrors if they become available. If you’re interested in setting up your own mirror in China, please contact flutter-dev@googlegroups.com for assistance. 