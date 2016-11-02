We aspire to reach a state where `flutter run` has the following modes:

- `flutter run`: builds a debug version of the app and starts it in "hot reload" mode via the loader, then shows the console UI to manipulate the running instance.
- `flutter run --no-hot`: builds a debug version of the app and starts it directly, then shows the console UI to manipulate the running instance.
- `flutter run --profile`: builds a profile version of the app and starts it directly, then shows the console UI to manipulate the running instance.
- `flutter run --release`: builds a release version of the app and starts it directly, then shows the console UI to manipulate the running instance.

- `flutter run --no-resident`: builds a debug version of the app and starts it directly, then quits.
- `flutter run --profile --no-resident`: builds a profile version of the app and starts it directly, then quits.
- `flutter run --release --no-resident`: builds a release version of the app and starts it directly, then quits.

- `flutter run`: builds a debug version of the app and starts it in "hot reload" mode via the loader; outputs only machine-readable messages on stdout and accepts machine commands on stdin.
- `flutter run --no-hot`: builds a debug version of the app and starts it directly; outputs only machine-readable messages on stdout and accepts machine commands on stdin.
- `flutter run --profile`: builds a profile version of the app and starts it directly; outputs only machine-readable messages on stdout and accepts machine commands on stdin.
- `flutter run --release`: builds a release version of the app and starts it directly; outputs only machine-readable messages on stdout and accepts machine commands on stdin.
