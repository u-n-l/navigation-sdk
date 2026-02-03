# Minimum Requirements
The Navigation SDK for Flutter enables the development of applications for Android and iOS. The minimum requirements for each platform are outlined below.

## Development Machine Requirements
Flutter must be correctly installed and configured according to the requirements for each target platform. For setup instructions, see the [Flutter Setup Guide](link://https://docs.flutter.dev/install).

The minimum supported verstions:
- **Dart**: v3.9.0 
- **Flutter**: v3.35.1

> 💡 **Tip:** Use the latest stable Flutter release and the Dart version bundled with it to ensure compatibility and avoid version conflicts.


The Navigation SDK for Flutter relies on following packages and versions:

- [plugin_platform_interface](link://https://pub.dev/packages/plugin_platform_interface) (version ^2.1.8)
- [logging](link://https://pub.dev/packages/logging) (version ^1.0.0)
- [ffi](link://https://pub.dev/packages/ffi) (version ^2.0.1).
- [meta](link://https://pub.dev/packages/meta) (version ^1.15.0).
- [flutter_lints](link://https://pub.dev/packages/flutter_lints) (version ^6.0.0).

Compatibility issues may occur if other project dependencies require different versions of these packages.


## Target Devices

The SDK is supported on both physical devices and emulators used for development.  
For positioning features, a device with GPS sensor support is required.

### Android
- Minimum Android API level: **27** (Android 8.1, Oreo), provifing compatibility with the majority of active Android devices.

### iOS
- Minimum OS version: **iOS 14.0 / iPadOS 14.0**

### Storage
- A basic compiled application may require approximately **250 MB** of storage.

> 📝 **Note:** On lower-end devices, performance may be reduced due to hardware limitations. This can impact UI smoothness and overall responsiveness, especially in complex or resource-intensive scenarios.