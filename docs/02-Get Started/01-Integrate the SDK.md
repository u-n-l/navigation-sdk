# Integrate the Navigation SDK

## Step 1: Add the Package
Add `unl_navigation_flutter` to your `pubsec.yaml`

```
dependencies:
  flutter:
    sdk: flutter
  unl_navigation_flutter:
  ```

  Then install:
  ```
  flutter pub get
 ```
    

## Step 2: Configure Your Platform
- **[Android](#android)**
- **[iOS](#iOS)**

### Android:
**Verify Android SDK**:

Ensure `ANDROID_SDK_ROOT` environment variable is set to your Android SDK path.

**Add Maven repository**:

In `android/build.gradle.kts`, add this inside the `allprojects` block:

```
allprojects {
    repositories {
        google()
        mavenCentral()
        maven {
            url = uri("https://developers.unl.global/packages/android")
        }
    }
}
```
  

**Disable code shrinking** (release builds only)

In android/app/build.gradle.kts, add these lines to the release block:
```
buildTypes {
    release {
        signingConfig = signingConfigs.getByName("debug")
        isMinifyEnabled = false
        isShrinkResources = false
    }
}
```

### iOS
**Install Dependencies**
```
flutter clean
flutter pub get
```
**Open Your Project in Xcode**
```

open ios/Runner.xcodeproj
```
**Set Minimum iOS version to 14.0**
In Xcode:

1. Select **Runner** (project navigator on the left)
2. Select **Runner Target**
3. Open **Build Settings** tab
4. Search for **iOS Deployment Target**
5. Change to **14.0** or higher

> **Note:** This SDK uses Swift Package Manager—no CocoaPods setup required.


## Troubleshooting
### Dependencies not installing:
Try reinstalling:
```
flutter clean
flutter pub get
```

Check for issues:
```
flutter doctor
```

### Android build is failing:
1. Open the android folder in Android Studio
2. Let Gradle sync
3. Fix any errors shown in the Build panel

### Still having issues
Clear the package cache and reinstall:
```
flutter pub cache clean
flutter pub get
```

### iOS app crashes on startup (release mode only)
Check that `ios/Runner/Info.plist` contains:
```
<key>CFBundleDevelopmentRegion</key>
<string>$(DEVELOPMENT_LANGUAGE)</string>
```

This entry is required for the SDK. Flutter projects include it by default.
