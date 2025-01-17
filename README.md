# Notes:

For my device (Samsung Galaxy S21) run: ./build.sh --arch arm64

You can also run this for debug: ./build.sh --arch arm64 --debug

If you don't have JDK 1.8 set already, do this in Android Studio:

File -> Settings -> Build, Execution, Deployment -> Build Tools -> Gradle.

Set Gradle JDK to download JDK and select version 1.8 and for example Eclipse Temurin.

Then sync gradle again (ctrl-shift-o).

(In commandline you can specify JDK with: ./gradlew -Dorg.gradle.java.home=/home/jonas/.jdks/temurin-1.8.0_392 assembleDebug)

To build release (I've tested nightly release):

Build -> Select Build Variant.

Select NightlyRelease, then run app in android studio and press fix on the suggested run configuration. Then build APK with:

Build -> Make Project

Also make sure that you have correct Android SDK and NDK installed. I compiled my project with these versions:

Android SDK Build-Tools: 34.0.0, 33.0.1, 29.0.2, 

NDK: 21.0.6113669 (90 % sure, but also double check buildscripts/include/version.sh) Android studio should tell you what version you need if you don't have the correct one.

CMake 3.22.1

Android SDK Platform-Tools:
34.0.5

To install different versions in Android Studio:

Tools -> SDK Manager -> SDK Tools -> Check Show package details and uncheck obsolete packages if needed.

# No longer under development

You can download one of the old builds from the Releases tab.

# OpenMW for Android

[Google Play](https://play.google.com/store/apps/details?id=is.xyz.omw) | [Google Play (Nightly)](https://play.google.com/store/apps/details?id=is.xyz.omw_nightly)

[F-Droid](https://f-droid.org/packages/is.xyz.omw/) | [F-Droid (Nightly)](https://f-droid.org/packages/is.xyz.omw_nightly/)

[FAQ & Info](https://omw.xyz.is/)

## Building

There are two steps for building OpenMW for Android. The first step is building C/C++ libraries. The second step is building the Java launcher.

### Prerequisites

You will need some standard tools installed that you probably already have (bash, gcc, g++, sha256sum, unzip).

CMake 3.6.0 or newer is **required**, you can download the latest version [here](https://cmake.org/download/) (and place in your `PATH`) if your distro ships with an outdated version.

Additionally, to build the launcher you will need Android SDK installed, it is suggested that you use Android Studio which can set it up for you (see step 2).

### Step 1: Build the libraries

Go into the `buildscripts` directory and run `./build.sh`. The script will automatically download the Android native toolchain and all dependencies, and will compile and install them.

### Step 2: Build the Java launcher

To get an APK file you can install, open the `openmw-android` directory in Android Studio and run the project.

Alternatively, if you do not have Android Studio installed or would rather not use it, run `./gradlew assembleDebug` from the root directory of this repository. The resulting APK, located at `./app/build/outputs/apk/debug/app-debug.apk`, can be transferred to the device and installed.

## Notes for developers

### Debugging native code

You can debug native code with `ndk-gdb`. To use it, once you've built both libraries and the apk and installed the apk, run the application and let it stay on the main menu. Then `cd` to `app/src/main` and run `./gdb.sh [arch]`. The `arch` variable has to match the library your device will be using (one of `arm`, `arm64`, `x86_64`, `x86`; `arm` is the default).

This also automatically enables gdb to use unstripped libraries, so you get proper symbols, source code references, etc.

### Running Address Sanitizer

To compile everything with ASAN:

```
# Clean previous build
./clean.sh
# Build with ASAN enabled & debug symbols
./build.sh --ccache --asan --debug
# Or: ./build.sh --ccache --asan --debug --arch arm64
```

Then open Android Studio and compile and install the project.

To get symbolized output:

```
adb logcat | ./tool/asan_symbolize.py --demangle -s ./symbols/armeabi-v7a/
# Or: adb logcat | ./tool/asan_symbolize.py --demangle -s ./symbols/arm64-v8a/
```

## Credits

### Source code

Original Java code written by sandstranger. Build scripts originally written by sandstranger and bwhaines.
