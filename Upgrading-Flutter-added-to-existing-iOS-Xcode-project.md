(This wiki page applies to people who added Flutter to an existing iOS application before August 2019.)

# Introduction
Prior to pull request [flutter/flutter#36793](https://github.com/flutter/flutter/pull/36793) merged on July 30, 2019 (`Flutter 1.8.4-pre.21`), adding Flutter to an existing iOS application required changes to the Podfile and adding a Run Script build phase to the existing Xcode project.  To update to Flutter past this pull request, you must alter your Podfile and delete the Run Script build phase.

The instructions have been updated on the [Add Flutter to existing apps](https://github.com/flutter/flutter/wiki/Add-Flutter-to-existing-apps#add-your-flutter-app-to-your-podfile) Wiki page. Ensure you are on the [master channel](https://github.com/flutter/flutter/wiki/Flutter-build-release-channels) and run 
`flutter upgrade` before following these instructions.

## Podfile

Previously, the following lines needed to be added to the `Podfile`:
 ```ruby
  flutter_application_path = 'path/to/my_flutter/'
  eval(File.read(File.join(flutter_application_path, '.ios', 'Flutter', 'podhelper.rb')), binding)
```

This should be updated to:
```ruby
  flutter_application_path = 'path/to/my_flutter/'
  load File.join(flutter_application_path, '.ios', 'Flutter', 'podhelper.rb')

  target 'MyApp' do
    install_all_flutter_pods(flutter_application_path)
  end
  target 'MyAppTests' do
    install_all_flutter_pods(flutter_application_path)
  end
```

## Build phase for building the Dart code

Previously, a build phase needed to be added to the existing Xcode project:
```sh
"$FLUTTER_ROOT/packages/flutter_tools/bin/xcode_backend.sh" build
"$FLUTTER_ROOT/packages/flutter_tools/bin/xcode_backend.sh" embed
```

This build phase is no longer necessary and must be deleted.
