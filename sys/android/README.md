# Build Instructions for Modern Android (Updated 2026)

## Preparations

### Prerequisites
- Download and extract the Android SDK tools: https://developer.android.com/studio
- **Download and extract the Android NDK r26b or later**: https://developer.android.com/ndk/downloads
  - **Important**: NDK r26b+ is required for 16KB page size support on modern Android devices
- Install JDK 11+ (JDK 8 is end-of-life)
- Install `bison` and `flex` for native NetHack build
- Clone this repository: `git clone https://github.com/jschmersal/NetHack-Android.git`
- Set `ANDROID_SDK_ROOT` environment variable to your Android SDK path
- Set `JAVA_HOME` environment variable to your JDK 11+ path

### Install Android Build Tools

1. Navigate to your Android SDK: `cd $ANDROID_SDK_ROOT/cmdline-tools/latest/bin`
2. Update the SDK manager: `./sdkmanager --update`
3. Install the latest platform tools: `./sdkmanager --install "platforms;android-34"`
4. Install NDK r26b: `./sdkmanager --install "ndk;26.1.10909125"`

## Build

### Build the Native NetHack Library (64-bit ARM)

1. `cd /path/to/NetHack-Android/sys/android`
2. Edit `Makefile.src` and update the NDK path:
   ```makefile
   NDK = /path/to/android-ndk-r26b
   ABI = arm64-v8a  # 64-bit ARM (required for modern Android)
   ```
3. `sh ./setup.sh`
4. `cd ../..`
5. `make install`

This will build the 64-bit NetHack library for ARM64 (arm64-v8a), required by Google Play and modern Android devices.

### Build the Android APK

1. `cd /path/to/NetHack-Android/sys/android`
2. Build the APK: `./gradlew build`
3. Find the APK at: `app/build/outputs/apk/debug/app-debug.apk`
4. Transfer to device and install: `adb install app/build/outputs/apk/debug/app-debug.apk`

### Build for Multiple ABIs (Optional)

To support both 32-bit (armeabi-v7a) and 64-bit (arm64-v8a), edit `sys/android/Makefile.src`:

```makefile
# Comment out arm64-v8a line and uncomment armeabi-v7a to switch ABIs
ABI = arm64-v8a     # Build for this first
#ABI = armeabi-v7a  # Then build this
```

Build both:
```bash
make clean && make install  # Builds arm64-v8a to app/libs/arm64-v8a/
# Edit Makefile.src to switch ABI
make clean && make install  # Builds armeabi-v7a to app/libs/armeabi-v7a/
```

The Gradle build will automatically include all ABIs from the `app/libs/` directory.

## What's Changed (Modern Android Support)

✅ **64-bit Support**: Primary ABI is now `arm64-v8a` (required for Google Play Store)
✅ **NDK r26b**: Updated from obsolete r19c to modern r26b with 16KB page size support
✅ **Gradle 8.0**: Updated from outdated 4.1.1 for compatibility with Android Studio
✅ **API Level 34**: Updated from API 30 to latest stable API level
✅ **JDK 11**: Updated from deprecated JDK 8 to JDK 11+
✅ **Repository Fix**: Replaced deprecated jcenter() with mavenCentral()
✅ **Multi-ABI Support**: Can build for arm64-v8a, armeabi-v7a, x86_64, x86

## Troubleshooting

### NDK not found error
Verify NDK path in `Makefile.src` matches your installation:
```bash
ls -la /path/to/android-ndk-r26b/toolchains/llvm/prebuilt/linux-x86_64/bin/
```

### Gradle build fails
- Ensure `ANDROID_SDK_ROOT` and `JAVA_HOME` are set correctly
- Run: `cd sys/android && ./gradlew clean build`

### Page size errors on modern devices
These are fixed by using NDK r26b and 64-bit ARM (arm64-v8a). Older NDK versions and 32-bit builds have this issue.

---

Happy hacking!
