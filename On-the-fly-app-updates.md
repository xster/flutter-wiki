# DEPRECATED

We no longer support app updates. We intend to add support in the future, but it is not a priority for now.

# Packaging your app

1. Generate a public/private key pair using the ECDSA algorithm. You can do this using the openssl commandline tool:

        openssl ecparam -genkey -name prime256v1 -out privatekey.der -outform DER

    Keep privatekey.der in a secret, safe place. This is your app's private key file, and you need it to release updates to your app.

2. In your app's top-level directory, create a file called flutter.yaml with at least the following fields:

        name: Your App's Name
        version: <semantic version number, like 1.0.1>
        update-url: <URL where you will host updates to your app>

3. Package your app with the flutter tool, being sure to pass it the path to your private key file. For example:

        flutter build --private-key=/path/to/privatekey.der --manifest=approot/flutter.yaml ...

This creates an app bundle file named `app.flx`, signed by your private key. Any updates must be signed by the same private key, or flutter will reject them. This means that anyone in possession of your private key can create a new version of your app, installable by users. So keep your key in a safe place!

# Releasing updates

When you want to release a new version of your app, you'll need to put a new `flutter.yaml` and `app.flx` in the directory pointed to by the `update-url` field in `flutter.yaml`. For example, if you have the following in your `flutter.yaml` file:

       update-url: https://example.com/myapp/

then both `https://example.com/myapp/flutter.yaml` and `https://example.com/myapp/app.flx` should be available.

`flutter.yaml` should be your app's `flutter.yaml` file, with a new version field. Make sure the version is semantically greater than the previous version, or Flutter won't know an update is ready.

`app.flx` should be the new version of your app bundle, as created by the `flutter build` tool.

# The updater

The first time your app is run, the Flutter runtime will register a periodic alarm (currently once per day) to check for updates. Each time this alarm fires, the updater will download `flutter.yaml` from your `update-url` and check whether its `version` field against the installed version. If the remote version is newer, Flutter will download a new `app.flx` from your `update-url`. If the new `app.flx` has a matching public key, and the signature verifies, Flutter installs the new `app.flx` in place of the currently installed version. The next time the user launches your app, they will see the new version.

If the update fails for any reason, an error message will be logged (viewable using `adb logcat`). The update will fail if any of the following situations occur:

1. Either `flutter.yaml` or `app.flx` fails to download from `update-url`.
2. `flutter.yaml` is invalid.
3. `app.flx` was signed with a different key than the installed app.
4. `app.flx` has an invalid signature.

# Differential updates

We currently don't support differential updates. The entire `app.flx` file is downloaded each time it is updated.