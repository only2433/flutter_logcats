# Flutter LogCats
A simple and powerful logging utility that enables viewing Flutter logs in Android native Logcat, making app maintenance much easier.

## 🚀 Installation

1. Add the `LogCats.dart` file to your project, preferably in the `lib/common` directory.

2. Update the package name in the `MethodChannel`:

```dart
static const MethodChannel _channel = MethodChannel('your_app_package_name/log');
// Replace 'your_app_package_name' with your actual app package name
```

3. Add the following code to your `MainActivity.kt` file located at `android/app/src/main/kotlin/your_app_package/MainActivity.kt`:

```kotlin
package your_app_package_name

import android.util.Log
import io.flutter.embedding.engine.FlutterEngine
import io.flutter.embedding.android.FlutterActivity
import io.flutter.plugin.common.MethodChannel

class MainActivity: FlutterActivity() {
    private val CHANNEL = "your_app_package_name/log"

    override fun configureFlutterEngine(flutterEngine: FlutterEngine) {
        super.configureFlutterEngine(flutterEngine)

        MethodChannel(
            flutterEngine.dartExecutor.binaryMessenger,
            CHANNEL
        ).setMethodCallHandler { call, result ->
            when (call.method) {
                "initialize" -> {
                    // Logger initialization (if needed)
                    result.success(null)
                }
                "log" -> {
                    val tag = call.argument<String>("tag") ?: "Flutter"
                    val level = call.argument<String>("level") ?: "INFO"
                    val message = call.argument<String>("message") ?: ""

                    // Call different logging methods based on log level
                    when (level) {
                        "INFO" -> Log.i(tag, message)
                        "WARN" -> Log.w(tag, message)
                        "ERROR" -> Log.e(tag, message)
                        else -> Log.d(tag, message)
                    }
                    result.success(null)
                }
                else -> {
                    result.notImplemented()
                }
            }
        }
    }
}
```

## 📋 Usage

Initialize LogCats in your app's main method:

```dart
void main() async {
  WidgetsFlutterBinding.ensureInitialized();
  await LogCats.initialize();
  runApp(MyApp());
}
```

Use the utility throughout your code:

```dart
// For info logs
LogCats.message("User signed in successfully");

// For warning logs
LogCats.warning("API rate limit approaching");

// For error logs
LogCats.error("Failed to connect to server");
```

### Example Output

When properly set up, you'll be able to see your Flutter logs in Android Studio's Logcat view with file names, function names, and line numbers like this:

