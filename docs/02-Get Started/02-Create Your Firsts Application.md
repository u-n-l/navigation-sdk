# Created Your First Application
Follow this tutorial to build a Flutter app with an interactive map.

## Step 1: Create a New Project
```
flutter create my_first_map_app
cd my_first_map_app
```

> 📝 **Note:** If you are using an existing project, skip to step 2 and add the Navigation SDK to your existing app.



## Step 2: Install the SDK
Add the package to `pubspec.yaml`:
```
dependencies:
  flutter:
    sdk: flutter
  unl_navigation_flutter:  # Add this line
```

Install the SDK:
```
flutter pub get
```

> 📝 **Note:** Platform configuration is required. Complete the Android/iOS setup before continuing (adds Maven repository, sets iOS version).

 ## Step 3: Write the Code
  Open `lib/main.dart` and replace everything with:

```
import 'package:flutter/material.dart' hide Route;
import 'package:unl_navigation_flutter/unl_navigation_flutter.dart';

const projectApiToken = String.fromEnvironment('GEM_TOKEN');

void main() {
  runApp(const MyApp());
}

class MyApp extends StatelessWidget {
  const MyApp({super.key});

  @override
  Widget build(BuildContext context) {
    return const MaterialApp(
      debugShowCheckedModeBanner: false,
      title: 'Hello Map',
      home: MyHomePage(),
    );
  }
}

class MyHomePage extends StatefulWidget {
  const MyHomePage({super.key});

  @override
  State<MyHomePage> createState() => _MyHomePageState();
}

class _MyHomePageState extends State<MyHomePage> {
  @override
  void dispose() {
    GemKit.release(); // Clean up SDK resources
    super.dispose();
  }

  @override
  Widget build(BuildContext context) {
    return Scaffold(
      appBar: AppBar(
        backgroundColor: Colors.deepPurple[900],
        title: const Text('Hello Map', style: TextStyle(color: Colors.white)),
      ),
      body: GemMap(
        appAuthorization: projectApiToken,
        onMapCreated: _onMapCreated,
      ),
    );
  }

  void _onMapCreated(GemMapController mapController) {
    // Map is initialized and ready to use
  }
}
```

### Understanding the Code

- `GemMap` - Widget that displays the interactive map
- `appAuthorization` - Your API key for SDK authentication
- `onMapCreated` - Called when the map finishes loading
- `GemKit.release()` - Frees memory when the app closes

## Step 4: Run Your App
Start the app with your API key:
```
flutter run --dart-define=GEM_TOKEN="your_actual_token_here"
```
> 💡 **Quick test:**  Hardcode the token by changing line 4 to: 
```
const projectApiToken = "your_actual_token_here";
```
> ⚠️ **Attention:** Never commit hardcoded tokens to version control!

> 📝 **Note:** When you initialize the SDK with a valid API key, it also performs an automatic activation. This allows better flexibility for licensing.

> ✅ Success! Your map app is running.




## Troubleshooting
### App Crashes Immediately
**Most common causes:**

- **Missing platform setup** - Did you complete the Android/iOS configuration?
- **Invalid API key** - Check your token is correct and active
- **Flutter environment issues** - Run flutter doctor and fix any issues

### GemKitUninitializedException error
You are calling SDK methods before the map initializes.

**Solution:** Only call SDK methods inside onMapCreated or after the map loads.

```
void _onMapCreated(GemMapController mapController) {
  // ✓ Safe to call SDK methods here
}
```

### How To Check If My API Key Is Valid
Add this code to verify your token:
```
SdkSettings.verifyAppAuthorization(projectApiToken, (status) {
  if (status == GemError.success) {
    print('✓ API key is valid');
  } else {
    print('✗ API key error: $status');
  }
});
```

Possible status codes:

- `success` - Token is valid ✓
- `invalidInput` - Wrong format ✗
- `expired` - Token expired ✗
- `accessDenied` - Token blocked ✗
    
    
### Map Shows a Watermark
This means your API key is missing or invalid.

Without a valid API key:

- ❌ Map downloads won't work
- ❌ Map updates disabled
- ⚠️ Watermark appears
- ⚠️ Limited features

Fix: Ensure you're passing a valid API key via `--dart-define=GEM_TOKEN="your_token"`

### Advanced: Manual SDK Initialization
The SDK auto-initializes when you create a `GemMap` widget.

If you need to use SDK features before showing a map:

```
void main() async {
  WidgetsFlutterBinding.ensureInitialized();
  await GemKit.initialize(appAuthorization: projectApiToken);
  
  runApp(const MyApp());
}

// Don't forget to clean up!
@override
void dispose() {
  GemKit.release();
  super.dispose();
}
```

When to use this:

- Background location tracking before map display
- Preloading map data
- SDK services without showing a map