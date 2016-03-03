This page describes the process for updating the material design icons:

 1. Use git to clone https://github.com/google/material-design-icons
 2. Create a fonts.zip file to upload to Google Storage:
  1. `cd iconfont` (i.e., your cloned copy of https://github.com/google/material-design-icons/tree/master/iconfont)
  2. `zip fonts.zip MaterialIcons-Regular.ttf codepoints ../LICENSE`
 3. Upload fonts.zip to Google Storage:
  1. Determine the sha1sum of `fonts.zip`.  By convention, we store the fonts at a location based on the sha1sum of the fonts.zip file, which you can determine as follows: `sha1sum fonts.zip`
  2. `gsutil cp fonts.zip gs://flutter_infra/flutter/fonts/<sha1>/fonts.zip`
 4. Update flutter.git to refer to the new fonts:
  1. Update [`bin/cache/material_fonts.version`](https://github.com/flutter/flutter/blob/master/bin/cache/material_fonts.version) to reference the location of the new fonts.zip.
  2. Update [`packages/flutter/lib/src/material/icons.dart`](https://github.com/flutter/flutter/blob/master/packages/flutter/lib/src/material/icons.dart) to reference the code point for any new icons.  The `codepoints` file that you included in `fonts.zip` describes the code points that exist in that version of the font.  Ideally we would have a script to automagically generate `icons.dart` from the `codepoints` file, but for now we're just doing it by hand.  (Note: you can diff the new `codepoints` file with the old one to see what's changed.)