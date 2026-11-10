# Local Android builds (no EAS Build)

This project uses Expo Prebuild and the local Android/Gradle toolchain. The native Android project has already been generated in `android/`. No Expo cloud build is configured or required.

## Development

```powershell
cd D:\Project\fitapp-application
# Correct the existing SDK environment mismatch for this terminal only.
$env:ANDROID_SDK_ROOT = $env:ANDROID_HOME
bun run android
```

This compiles and installs the development build on a connected Android device or emulator. For subsequent JavaScript development:

```powershell
bun start
```

Regenerate native Android configuration after changing Expo plugins or app configuration:

```powershell
bun run prebuild
```

Avoid `--clean` if you have manual native changes that are not captured by config plugins.

## Build an APK without installing it

```powershell
cd D:\Project\fitapp-application
$env:ANDROID_SDK_ROOT = $env:ANDROID_HOME
# Keep future Gradle downloads/caches off the full C: drive.
$env:GRADLE_USER_HOME = 'D:\BuildCache\gradle'
New-Item -ItemType Directory -Force $env:GRADLE_USER_HOME | Out-Null
cd android
.\gradlew.bat :app:assembleDebug
```

Expected output after a successful build: `android/app/build/outputs/apk/debug/app-debug.apk`.
This is a debug/development APK, not a production release. The development client uses Metro (`bun start`). Production releases require release signing configuration and a release build.

## Current verification and blocker

- Android prebuild: successful.
- Gradle wrapper: starts successfully (Gradle 9.4.1 with the configured JDK 17).
- TypeScript: passed before the native build attempt.
- APK build: NOT successful. The first build failed while creating an instrumented Kotlin plugin JAR in the C: Gradle cache. A subsequent diagnostic failed writing its log with `No space left on device`; drive C: was confirmed to have zero free bytes.
- Free space on C: before continuing. No files were deleted automatically. Moving future Gradle caches to D: does not remove the existing C: cache or restore C: free space.
- The configured `ANDROID_HOME` points to an existing SDK under `C:/Users/LuckyData/AppData/Local/Android/Sdk`; the inherited `ANDROID_SDK_ROOT` points to a nonexistent SDK. `android/local.properties` records the existing SDK; the terminal commands above also align the environment variables without changing global Windows settings.

Local Android builds still download dependencies and potentially missing Android SDK components. They do not require an Expo account or EAS cloud build. Local iOS compilation requires macOS and Xcode.
