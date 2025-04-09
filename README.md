# Flutter LogCats
A simple and powerful logging utility that enables viewing Flutter logs in Android native Logcat, making app maintenance much easier.
<br><br>

## 📝 Overview
Flutter development has historically presented challenges when it comes to log visibility within Android's native debugging ecosystem. This advanced logging utility addresses this critical pain point by establishing a bidirectional communication channel between your Flutter application and Android's native Logcat, enabling comprehensive log monitoring with rich contextual information.
<br><br>
## 🚀 Installation

1. Integrate the `LogCats.dart` utility into your project architecture, preferably within the `lib/common` directory for optimal organization.

2. Configure the `MethodChannel` with your application's package namespace:

```dart
static const MethodChannel _channel = MethodChannel('your_app_package_name/log');
// Replace with your application's package identifier
```

3. Implement the native platform handler in your `MainActivity.kt` file located at `android/app/src/main/kotlin/your_app_package/MainActivity.kt`:

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
<br><br>
## 📋 Implementation

Initialize the LogCats subsystem during your application bootstrap sequence:

```dart
void main() async {
  WidgetsFlutterBinding.ensureInitialized();
  await LogCats.initialize();
  runApp(MyApp());
}
```

Leverage the utility throughout your codebase with the intuitive API:

```dart
// For informational logging
LogCats.message("Authentication successful: User session established");

// For warning indicators
LogCats.warning("API rate limit threshold approaching (80%)");

// For critical error reporting
LogCats.error("Connection to authentication service failed: Timeout exceeded");
```
### Runtime Output

When properly integrated, your Flutter application logs will appear in Android Studio's Logcat console with comprehensive metadata and contextual information:
<br>
<img src="https://private-user-images.githubusercontent.com/10841533/431610689-5d2ea71f-be84-412d-9740-4854fadb5081.png?jwt=eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJpc3MiOiJnaXRodWIuY29tIiwiYXVkIjoicmF3LmdpdGh1YnVzZXJjb250ZW50LmNvbSIsImtleSI6ImtleTUiLCJleHAiOjE3NDQxNTc0MDUsIm5iZiI6MTc0NDE1NzEwNSwicGF0aCI6Ii8xMDg0MTUzMy80MzE2MTA2ODktNWQyZWE3MWYtYmU4NC00MTJkLTk3NDAtNDg1NGZhZGI1MDgxLnBuZz9YLUFtei1BbGdvcml0aG09QVdTNC1ITUFDLVNIQTI1NiZYLUFtei1DcmVkZW50aWFsPUFLSUFWQ09EWUxTQTUzUFFLNFpBJTJGMjAyNTA0MDklMkZ1cy1lYXN0LTElMkZzMyUyRmF3czRfcmVxdWVzdCZYLUFtei1EYXRlPTIwMjUwNDA5VDAwMDUwNVomWC1BbXotRXhwaXJlcz0zMDAmWC1BbXotU2lnbmF0dXJlPWZhMDc0MTI1NzM4ZDIyOWM4ZDQ5Y2Q0YzRmNTYwNzliYzMwODM1Mzg2MWIzZTUzYWUzNDUwNDBkYzU0OGJiODAmWC1BbXotU2lnbmVkSGVhZGVycz1ob3N0In0.ZIFKhcPN5lmBIqW79GAu4EeI0OGwwv2UciKikQak1bQ" width="1000" height="300"/>

<br><br>
## ✨ Features

- Simple, easy-to-use logging API
- Automatic inclusion of file name, function name, and line number
- Formatted timestamps for better log readability
- Log level support (INFO, WARN, ERROR)
- Direct integration with Android's native Logcat

<br><br>
## 내용 관련
: 본 문서는 의도적으로 한국어가 아닌 영어로 작성되었습니다.<br> 이는 전 세계 다양한 개발자들이 이 로깅 유틸리티를 쉽게 사용할 수 있도록 하기 위함입니다.
