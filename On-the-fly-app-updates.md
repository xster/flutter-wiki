TODO: Write proper docs.

- Add an 'update-url' and a 'version' field to the package's flutter.yaml file. E.g. https://github.com/flutter/flutter/blob/master/examples/fitness/flutter.yaml
- In the directory pointed to by update-url, place 2 files: your app's 'flutter.yaml' and 'app.flx'.
- Periodically (once a day), flutter will check for updates by downloading a new flutter.yaml from the update-url. If the version number has changed, we'll download app.flx.
- We check the app.flx signature to ensure it's the right package.
- Signing works like so:
-- Generate an EC-DSA signature using openssl:
    openssl ecparam -genkey -name prime256v1 -out privatekey.der -outform DER
-- When you package your flutter app with the flutter tool, make sure to pass the flag --private-key=privatekey.der (actually this may have changed with some recent changes to the flutter tool. I'll have to check.)