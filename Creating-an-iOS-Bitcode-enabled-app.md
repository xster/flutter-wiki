The instructions in this document require that you use an engine build from after [flutter/engine@8f060b9](https://github.com/flutter/engine/commit/8f060b99547944606c9f5a7b1f0daea13b94f86e) and a Framework build from after [flutter/flutter#36471](https://github.com/flutter/flutter/issues/36471).

First, make sure you have followed the steps at [Setting up the Engine development environment](https://github.com/flutter/flutter/wiki/Setting-up-the-Engine-development-environment).  It would also be good to review [Compiling the Engine for iOS from macOS](https://github.com/flutter/flutter/wiki/Compiling-the-engine#compiling-for-ios-from-macos).

Bitcode requires that all libraries being built have bitcode, and that they're all built with compatible toolchains. Building the engine with bitcode will require using the same Xcode toolchain as you use to build your final app. If you are a Googler, GOMA will not work for this - you will have to just do a normal no-goma build.

Bitcode takes longer to build.  It is normally only worth doing with release and profile applications, but you can build the engine in debug mode with bitcode to be able to link it in with your debug mode bitcode enabled Xcode project.

In your `engine/src` directory, run the following commands:

```bash
src$ ./flutter/tools/gn --ios --runtime-mode=profile --no-goma --bitcode
src$ ./flutter/tools/gn --runtime-mode=profile # The host does not have to be built with bitcode, and can use GOMA
src$ autoninja -C out/ios_profile
src$ autoninja -C out/host_profile
```

In your Xcode project, ensure that `ENABLE_BITCODE` is set to `YES` for all targets. See this [StackOverflow question](https://stackoverflow.com/questions/31205133/how-to-enable-bitcode-in-xcode-7) if you're not sure how to do that - the steps are still roughly the same in Xcode 10. If you are using plugins, you will also have to do this step after each Pod install to prevent the framework from resetting this value on you, and remove any `config.build_settings['ENABLE_BITCODE'] = 'NO'` from the Podfile.

You should then be able to build your application using a `--local-engine` flag.  E.g.

```bash
my_flutter_app$ flutter build ios --profile --local-engine=ios_profile
```

And it should build with bitcode. 

You can validate this by creating an archive for the product in Xcode and creating distribution artifacts for local development that use bitcode recompilation.
