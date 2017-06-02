## Flutter templates

This page lists the files available in the Flutter templates, and explains why they are present.

### Flutter application

Created with `flutter create [-a kotlin] [-i swift] <appname>`.

[App template source](https://github.com/flutter/flutter/tree/master/packages/flutter_tools/templates/create).

| File                                                                        | Required by            | Reason needed              | User editable | Tool editable |
|-----------------------------------------------------------------------------|------------------------|----------------------------|---------------|---------------|
| .gitignore                                                                  | git                    | Clean dirty state          | Yes           |               |
| .idea/libraries/Dart_SDK.xml                                                | IntellIJ               | Dart plugin                | No            | Yes           |
| .idea/libraries/Flutter_for_Android.xml                                     | IntellIJ               | Flutter plugin             | No            | Yes           |
| .idea/modules.xml                                                           | IntellIJ               | Modules in the project     | No            | Yes           |
| .idea/runConfigurations/main_dart.xml                                       | IntellIJ               | What to launch             | No            | Yes           |
| .idea/workspace.xml                                                         | IntellIJ               | Files to initially open    | No            | Yes           |
| android-java/app/build.gradle                                               | Gradle (Android build) | Android build settings     | Yes           |               |
| android-java/app/src/main/java/organization/projectName/MainActivity.java   | Developer              | Native source code         | Yes           |               |
| android-java/build.gradle                                                   | Gradle (Android build) | Android build settings     | Yes           |               |
| android-kotlin/app/build.gradle                                             | Gradle (Android build) | Android build settings     | Yes           |               |
| android-kotlin/app/src/main/kotlin/organization/projectName/MainActivity.kt | Developer              | Main iOS entrypoint        | Yes           |               |
| android-kotlin/build.gradle                                                 | Gradle (Android build) | Android build settings     | Yes           |               |
| android.iml                                                                 | IntelliJ               | Main IntelliJ project file | No            | Yes           |
| android/.gitignore                                                          | git                    | Clean dirty state          | Yes           |               |
| android/app/src/main/AndroidManifest.xml                                    | Developer              | Main Android app manifest  | Yes           | Yes           |
| android/app/src/main/res/mipmap-hdpi/<br>ic_launcher.png                    | Developer              | App icon                   | Yes           |               |
| android/app/src/main/res/mipmap-mdpi/<br>ic_launcher.png                    | Developer              | App icon                   | Yes           |               |
| android/app/src/main/res/mipmap-xhdpi/<br>ic_launcher.png                   | Developer              | App icon                   | Yes           |               |
| android/app/src/main/res/mipmap-xxhdpi/<br>ic_launcher.png                  | Developer              | App icon                   | Yes           |               |
| android/app/src/main/res/mipmap-xxxhdpi/<br>ic_launcher.png                 | Developer              | App icon                   | Yes           |               |
| android/gradle.properties                                                   | Gradle (Android build) | Android build settings     | Yes           |               |
| android/settings.gradle                                                     | Gradle (Android build) | Android build settings     | Yes           |               |
| ios-objc/Podfile                                                            | Cocoapods              | Remove? Bug 9827           | No            | Yes           |
| ios-objc/Runner.xcodeproj/project.pbxproj                                   | Xcode                  | Xcode project settings     | Yes           | Yes           |
| ios-objc/Runner/AppDelegate.h                                               | Developer              | Main iOS entrypoint        | Yes           |               |
| ios-objc/Runner/AppDelegate.m                                               | Developer              | Main iOS entrypoint        | Yes           |               |
| ios-objc/Runner/main.m                                                      | Developer              | Main iOS entrypoint        | Yes           |               |
| ios-swift/Podfile                                                           | Cocoapods              | Remove? Bug 9827           | No            | Yes           |
| ios-swift/Runner.xcodeproj/project.pbxproj                                  | Xcode                  | Xcode project settings     | Yes           | Yes           |
| ios-swift/Runner/AppDelegate.swift                                          | Developer              | Main iOS entrypoint        | Yes           |               |
| ios-swift/Runner/Runner-Bridging-Header.h                                   | Xcode                  | Enable Swift support       | Yes           |               |
| ios/.gitignore                                                              | git                    | Clean dirty state          | Yes           |               |
| ios/Flutter/AppFrameworkInfo.plist                                          | Xcode                  | Flutter manifest           | Not sure      | Not sure      |
| ios/Flutter/Debug.xcconfig                                                  | Xcode                  | Xcode project settings     | Yes           | Yes           |
| ios/Flutter/Release.xcconfig                                                | Xcode                  | Xcode project settings     | Yes           | Yes           |
| ios/Runner.xcodeproj/project.xcworkspace/contents.xcworkspacedata           | Xcode                  | Xcode project settings     | Yes           | Yes           |
| ios/Runner.xcodeproj/xcshareddata/xcschemes/Runner.xcscheme                 | Xcode                  | Xcode project settings     | Yes           | Yes           |
| ios/Runner.xcworkspace/contents.xcworkspacedata                             | Xcode                  | Xcode project settings     | Yes           | Yes           |
| ios/Runner/Assets.xcassets/AppIcon.appiconset/<br>Contents.json             | Developer              | App icon                   | Yes           |               |
| ios/Runner/Assets.xcassets/AppIcon.appiconset/<br>Icon-App-20x20@1x.png     | Developer              | App icon                   | Yes           |               |
| ios/Runner/Assets.xcassets/AppIcon.appiconset/<br>Icon-App-20x20@2x.png     | Developer              | App icon                   | Yes           |               |
| ios/Runner/Assets.xcassets/AppIcon.appiconset/<br>Icon-App-20x20@3x.png     | Developer              | App icon                   | Yes           |               |
| ios/Runner/Assets.xcassets/AppIcon.appiconset/<br>Icon-App-29x29@1x.png     | Developer              | App icon                   | Yes           |               |
| ios/Runner/Assets.xcassets/AppIcon.appiconset/<br>Icon-App-29x29@2x.png     | Developer              | App icon                   | Yes           |               |
| ios/Runner/Assets.xcassets/AppIcon.appiconset/<br>Icon-App-29x29@3x.png     | Developer              | App icon                   | Yes           |               |
| ios/Runner/Assets.xcassets/AppIcon.appiconset/<br>Icon-App-40x40@1x.png     | Developer              | App icon                   | Yes           |               |
| ios/Runner/Assets.xcassets/AppIcon.appiconset/<br>Icon-App-40x40@2x.png     | Developer              | App icon                   | Yes           |               |
| ios/Runner/Assets.xcassets/AppIcon.appiconset/<br>Icon-App-40x40@3x.png     | Developer              | App icon                   | Yes           |               |
| ios/Runner/Assets.xcassets/AppIcon.appiconset/<br>Icon-App-60x60@2x.png     | Developer              | App icon                   | Yes           |               |
| ios/Runner/Assets.xcassets/AppIcon.appiconset/<br>Icon-App-60x60@3x.png     | Developer              | App icon                   | Yes           |               |
| ios/Runner/Assets.xcassets/AppIcon.appiconset/<br>Icon-App-76x76@1x.png     | Developer              | App icon                   | Yes           |               |
| ios/Runner/Assets.xcassets/AppIcon.appiconset/<br>Icon-App-76x76@2x.png     | Developer              | App icon                   | Yes           |               |
| ios/Runner/Assets.xcassets/AppIcon.appiconset/<br>Icon-App-83.5x83.5@2x.png | Developer              | App icon                   | Yes           |               |
| ios/Runner/Base.lproj/LaunchScreen.storyboard                               | Developer              | Launchscreen source        | Yes           |               |
| ios/Runner/Base.lproj/Main.storyboard                                       | Developer              | Main iOS entrypoint        | Yes           |               |
| ios/Runner/Info.plist                                                       | Developer              | Main Android app manifest  | Yes           | Yes           |
| lib/main.dart                                                               | Dart                   | Main Dart entrypoint       | Yes           |               |
| projectName_android.iml                                                     | IntellIJ               | Main IntelliJ project file | No            | Yes           |
| projectName.iml                                                             | IntellIJ               | Main IntelliJ project file | No            | Yes           |
| pubspec.yaml                                                                | Dart                   | List dependencies          | Yes           |               |
| README.md                                                                   | git                    | Clean dirty state          | Yes           |               |

### Flutter plugin

Created with `flutter create --plugin [-a kotlin] [-i swift] <appname>`. 

[Plugin template source](https://github.com/flutter/flutter/tree/master/packages/flutter_tools/templates/plugin) combined with [App template source](https://github.com/flutter/flutter/tree/master/packages/flutter_tools/templates/create) inside `/example/`.

