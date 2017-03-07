Flutter on Windows is brand-new. **Expect some sharp edges when you try it out.** If you experience any issues, please file a [bug on GitHub](Flutter on Windows is brand-new. Expect some sharp edges when you try it out. If you experience any issues, please file a bug on GitHub. For Windows-related issues, feel free to mention "@goderbauer" in your bug report.). For Windows-related issues, feel free to mention "@goderbauer" in your bug report.

## Known Issues On Windows:
* Hot Reload is slower than it is on other platforms ([#8513](https://github.com/flutter/flutter/issues/8513))
* The flutter test command is not supported ([#8516](https://github.com/flutter/flutter/issues/8516))

For other smaller issues, see our [bug tracker on GitHub](https://github.com/flutter/flutter/issues?q=is%3Aopen+is%3Aissue+label%3A%22%E2%9D%96+platform-windows%22).

## Installation Instructions
1. [Download](https://git-scm.com/download/win) and install Git for Windows.
   * Make sure to select "Use git from the Windows Command Prompt" in the installer.
   * If Git is already installed on your system, make sure you can run `git` from PowerShell and/or the regular Cmd command prompt.
1. [Download](http://www.oracle.com/technetwork/java/javase/downloads/index.html) and install the Java Platform JDK 8.
1. Download and install Android Studio as explained in the [Android setup instructions](https://flutter.io/setup/#android-setup).
1. Open a fresh (!) PowerShell (recommend) or Cmd command prompt window and clone the Flutter repository with the following command:
   * `git clone https://github.com/flutter/flutter.git`
   * This creates a `flutter` sub-directory in the current directory.
1. Add the full path to the `flutter/bin` directory created by the previous step to your `PATH` environment variable as follows:
   * If you have admin rights on your machine:
      1. Control Panel > System and Security > System > Advanced system settings > Environment Variables...
      1. Modify the `PATH` system variable to include the full path to `flutter/bin`.
   * Alternatively, if you don't have admin rights on your machine:
      1. Control Panel > User Accounts > User Accounts > Change my environment variables
      1. Add a `PATH` user variable with the full path to `flutter/bin` as value
1. Open a fresh (!) PowerShell (recommend) or Cmd command prompt window and execute `flutter doctor`. It should tell you that everything is ok with your Flutter installation (minus the IDE, see next step).
   * The `flutter` command works in both PowerShell and the regular Cmd prompt. However, we recommend that you use PowerShell because it will give you better/fancier output.
   * If PowerShell complains that the Flutter script cannot be executed, modify your [execution policy](https://msdn.microsoft.com/en-us/powershell/reference/5.1/microsoft.powershell.core/about/about_execution_policies) with the following command: `Set-ExecutionPolicy RemoteSigned`. Alternatively, you can just use the regular Cmd command prompt.
1. Optional: Install an IDE with the Dart and Flutter plugin ([instructions](https://flutter.io/intellij-setup/)).

With that, you are ready to create your first Flutter app: [Getting Started](https://flutter.io/getting-started/).