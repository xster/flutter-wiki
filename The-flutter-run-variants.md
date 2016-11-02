We aspire to reach a state where `flutter run` has the following modes:

- `flutter run`: builds a debug version of the app and starts it in "hot reload" mode via the loader, then shows the console UI to manipulate the running instance.
- `flutter run --no-hot`: builds a debug version of the app and starts it directly, then shows the console UI to manipulate the running instance.
- `flutter run --profile`: builds a profile version of the app and starts it directly, then shows the console UI to manipulate the running instance.
- `flutter run --release`: builds a release version of the app and starts it directly, then shows the console UI to manipulate the running instance.

- `flutter run --no-resident`: builds a debug version of the app and starts it directly, then quits.
- `flutter run --profile --no-resident`: builds a profile version of the app and starts it directly, then quits.
- `flutter run --release --no-resident`: builds a release version of the app and starts it directly, then quits.

Adding `--machine` in any of the situations above
* changes the output to JSON so that it can be more easily consumed by IDEs, and
* allows the use of JSON commands to interact with the running application (e.g. stopping the application).