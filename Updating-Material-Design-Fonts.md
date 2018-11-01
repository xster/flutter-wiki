_(This page is referenced by comments in the Flutter codebase.)

This page describes the process for updating the material design icons:

 1. Use git to clone ~~https://github.com/google/material-design-icons~~ https://goto.google.com/flutter-material-font (Google-only, sorry) and https://github.com/google/fonts
 2. Create a fonts.zip file to upload to Google Storage:
```
    STAGING=/tmp/fonts_staging
    mkdir $STAGING
    cp material-design-icons/iconfont/{MaterialIcons-Regular.ttf,codepoints} $STAGING
    cp material-design-icons/LICENSE $STAGING/MaterialIcons_LICENSE
    cp fonts/apache/roboto/*.ttf $STAGING
    cp fonts/apache/roboto/LICENSE.txt $STAGING/Roboto_LICENSE.txt
    cp fonts/apache/robotocondensed/*.ttf $STAGING
    cp fonts/apache/robotocondensed/LICENSE.txt $STAGING/RobotoCondensed_LICENSE.txt
    zip -j fonts.zip $STAGING/*
```

 3. Upload fonts.zip to Google Storage:
  1. Determine the sha1sum of `fonts.zip`.  By convention, we store the fonts at a location based on the sha1sum of the fonts.zip file, which you can determine as follows: `sha1sum fonts.zip`
  2. `gsutil cp fonts.zip gs://flutter_infra/flutter/fonts/<sha1>/fonts.zip`
 4. Update flutter.git to refer to the new fonts:
  1. Update [`bin/internal/material_fonts.version`](https://github.com/flutter/flutter/blob/master/bin/internal/material_fonts.version) to reference the location of the new fonts.zip.
  2. Run [`dev/tools/update_icons.dart`](https://github.com/flutter/flutter/blob/master/dev/tools/update_icons.dart) to update [`packages/flutter/lib/src/material/icons.dart`](https://github.com/flutter/flutter/blob/master/packages/flutter/lib/src/material/icons.dart) to reference the code point for any new icons.  The `codepoints` file that you included in `fonts.zip` describes the code points that exist in that version of the font.