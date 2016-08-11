With Flutter's master branch as of August 11th, 2016, we now have an _early access_ auto-formatting solution. We are looking for feedback from users. Please try it out and [file issues](https://github.com/flutter/flutter/issues/new).

(Thanks to the Dart team for their help with the _dartfmt_ utility.)

## Prereqs

Make sure you are using a version of Flutter that is newer than August 11th, 2016. You can check this by going to your local copy of the flutter repo, and looking at the top result from `git log`.

Also, make sure you are using `dartfmt` from `$FLUTTER_ROOT/bin/cache/dart-sdk/bin/dartfmt`. It should be version `0.2.8` or newer, which you can check with `dartfmt --version`.

If you are using Atom, please ensure your Flutter plugin for Atom has the correct `flutter_root` setting.
Also, you may want to enable "format on save" option in the Dart plugin settings.

[[images/formatting_settings.png]]

## Using trailing commas

To get a good formatting experience, you must first adopt _trailing commas_. (The reasons are out of the scope of this article.)

[[images/trailing_commas.png]]
