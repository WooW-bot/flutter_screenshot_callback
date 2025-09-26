# flutter_screenshot_callback_plus

A Flutter plugin for detecting screenshot events on Android and iOS platforms. This is an enhanced version of the original screenshot callback plugin with improved compatibility and features.

## Features

- 📱 Cross-platform screenshot detection (Android & iOS)
- 🔧 Easy to integrate and use
- 🎯 Real-time screenshot event callbacks
- 🛡️ Permission handling for Android storage access
- 💪 Enhanced stability and performance

## Platform Support

| Platform | Support |
|----------|---------|
| Android  | ✅      |
| iOS      | ✅      |

## Installation

Add this to your package's `pubspec.yaml` file:

```yaml
dependencies:
  flutter_screenshot_callback_plus: ^1.0.0
```

Run:
```bash
flutter pub get
```

## Usage

### Import the plugin

```dart
import 'package:flutter_screenshot_callback_plus/flutter_screenshot_callback_plus.dart';
```

### Basic Implementation

```dart
class MyApp extends StatefulWidget {
  @override
  _MyAppState createState() => _MyAppState();
}

class _MyAppState extends State<MyApp> implements IScreenshotCallback {
  String _imagePath = 'Unknown';
  late ScreenshotCallback _screenshotCallback;

  @override
  void initState() {
    super.initState();
    initCallback();
  }

  void initCallback() {
    _screenshotCallback = ScreenshotCallback();
    _screenshotCallback.startScreenshot();
    _screenshotCallback.setInterfaceScreenshotCallback(this);
  }

  @override
  void dispose() {
    super.dispose();
    _screenshotCallback.stopScreenshot();
  }

  @override
  screenshotCallback(String data) {
    setState(() {
      _imagePath = data;
    });
    print('Screenshot taken: $data');
  }

  @override
  deniedPermission() {
    print('Permission denied for screenshot detection');
  }

  @override
  Widget build(BuildContext context) {
    return MaterialApp(
      home: Scaffold(
        appBar: AppBar(
          title: const Text('Screenshot Callback Plus'),
        ),
        body: Center(
          child: Text('Image path: $_imagePath\n'),
        ),
      ),
    );
  }
}
```

### Advanced Usage with App Lifecycle

For better performance, especially on Samsung devices, handle app lifecycle states:

```dart
@override
void didChangeAppLifecycleState(AppLifecycleState state) {
  switch (state) {
    case AppLifecycleState.resumed: // App visible, foreground
      _screenshotCallback.startScreenshot();
      break;
    case AppLifecycleState.paused: // App invisible, background
      _screenshotCallback.stopScreenshot();
      break;
    default:
      break;
  }
}
```

## Android Configuration

### Permissions

The plugin automatically includes the necessary permissions in your `android/app/src/main/AndroidManifest.xml`:

```xml
<uses-permission android:name="android.permission.READ_EXTERNAL_STORAGE"/>
<uses-permission android:name="android.permission.WRITE_EXTERNAL_STORAGE"/>
<!-- For Android 13+ -->
<uses-permission android:name="android.permission.READ_MEDIA_IMAGES" />
<uses-permission android:name="android.permission.READ_MEDIA_VIDEO" />
```

### Runtime Permissions

Make sure to request storage permissions at runtime for Android devices. The plugin will call `deniedPermission()` if permissions are not granted.

## iOS Configuration

No additional configuration is required for iOS. The plugin uses the system notification `UIApplication.userDidTakeScreenshotNotification` to detect screenshots.

## Important Notes

- 🔴 **Real Device Required**: Screenshot detection only works on real devices, not simulators/emulators
- 📱 **Samsung Devices**: On Samsung devices, the side screenshot gesture may trigger app lifecycle changes
- 🔒 **Permissions**: Android requires storage permissions to function properly
- 📂 **File Path**: The callback returns the path where the screenshot is temporarily stored

## API Reference

### ScreenshotCallback

Main class for screenshot detection.

#### Methods

- `startScreenshot()` - Start listening for screenshot events
- `stopScreenshot()` - Stop listening for screenshot events
- `setInterfaceScreenshotCallback(IScreenshotCallback callback)` - Set the callback interface

### IScreenshotCallback

Interface for handling screenshot events.

#### Methods

- `screenshotCallback(String data)` - Called when a screenshot is detected with the file path
- `deniedPermission()` - Called when storage permission is denied (Android only)

## Troubleshooting

### Android Issues

1. **Permission Denied**: Ensure storage permissions are granted
2. **No Callback**: Check if the app has proper storage access
3. **Samsung Devices**: Implement app lifecycle handling for better reliability

### iOS Issues

1. **No Detection**: Ensure you're testing on a real device, not simulator
2. **Late Callbacks**: iOS screenshot detection may have slight delays

## Example

Check the `example` folder for a complete working example.

## Contributing

Contributions are welcome! Please feel free to submit a Pull Request.

## License

This project is licensed under the MIT License - see the [LICENSE](LICENSE) file for details.

## Changelog

### 1.0.0
- Enhanced version of flutter_screenshot_callback
- Updated for Flutter 3.x compatibility
- Improved Android 13+ support
- Better error handling and stability
- Updated dependencies and build configurations