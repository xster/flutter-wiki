To update Flutter's dependencies, run the following command in a clean checkout of Flutter:

```
flutter update-packages --force-upgrade
```

This will update your `pubspec.yaml` files. You should then run all the tests, check the diff to make sure it's what you would expect, and submit a PR.