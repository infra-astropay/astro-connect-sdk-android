# AstroConnectSDK - Android

SDK for integrating AstroPay Connect into Android applications.

## Requirements

- Android SDK 24+ (Android 7.0)
- Kotlin 1.9+
- Jetpack Compose

## Installation

### Option 1: Maven Repository (Recommended)

Add the AstroPay Maven repository to your project's `settings.gradle.kts`:

```kotlin
dependencyResolutionManagement {
    repositories {
        google()
        mavenCentral()
        maven { url = uri("https://infra-astropay.github.io/astro-connect-sdk-android/") }
    }
}
```

Then add the dependency to your app's `build.gradle.kts`:

```kotlin
dependencies {
    implementation("com.astropay:connect:1.0.0")
}
```

### Option 2: Manual Integration - Using AAR

To integrate AstroConnectSDK manually into your Android project:

1. Copy `astro-connect-sdk-{VERSION}.aar` (e.g., `astro-connect-sdk-1.0.0.aar`) to your app's `libs/` folder
2. Add the following to your app's `build.gradle.kts`:

```kotlin
dependencies {
    implementation(files("libs/astro-connect-sdk-{VERSION}.aar"))

    // Required dependencies
    implementation("androidx.core:core-ktx:1.12.0")
    implementation("androidx.appcompat:appcompat:1.6.1")
    implementation("com.google.android.material:material:1.11.0")
    implementation("androidx.activity:activity-ktx:1.8.2")

    // Compose
    implementation(platform("androidx.compose:compose-bom:2024.02.00"))
    implementation("androidx.compose.ui:ui")
    implementation("androidx.compose.material3:material3")
    implementation("androidx.activity:activity-compose:1.8.2")
}
```

## Configuration

### Required Permissions

Add the following permissions to your `AndroidManifest.xml` if the flow requires camera access:

```xml
<uses-permission android:name="android.permission.CAMERA" />
<uses-permission android:name="android.permission.INTERNET" />
```

### Create Configuration

```kotlin
import com.astropay.connect.core.AstroConfiguration
import com.astropay.connect.core.AstroTheme
import com.astropay.connect.core.AstroLogSetting
import com.astropay.connect.core.AstroLogLevel

// Using Builder pattern
val configuration = AstroConfiguration.builder()
    .setEnvironment("sandbox")                // "sandbox", "production"
    .setAppIssuer("your-app-issuer")          // Application identifier (your app name)
    .setAccessToken("your-access-token")      // Authentication token
    .setTheme(AstroTheme.SYSTEM)              // LIGHT, DARK, SYSTEM (optional)
    .setLanguage("en")                        // Language code (optional, default: "en")
    .setFlow("home")                          // Specific flow (optional)
    .setFlowParams(mapOf("amount" to 100))    // Flow parameters (optional)
    .setEmbedded(true)                        // Embedded mode (optional, default: true)
    .setLogSetting(AstroLogSetting(           // Log configuration (optional)
        enabled = true,
        logLevel = AstroLogLevel.DEBUG
    ))
    .build()

// Or using data class directly
val configuration = AstroConfiguration(
    environment = "sandbox",
    appIssuer = "your-app-issuer",
    accessToken = "your-access-token",
    theme = AstroTheme.SYSTEM,
    language = "en"
)
```

### Configuration Parameters

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `environment` | `String` | Yes | Environment: `"sandbox"`, `"production"` |
| `appIssuer` | `String` | Yes | Application identifier |
| `accessToken` | `String` | No* | Authentication token. *Required on first use to initiate session; optional afterwards |
| `theme` | `AstroTheme` | No | Visual theme: `LIGHT`, `DARK`, `SYSTEM` |
| `language` | `String` | No | Language code (e.g., `"en"`, `"es"`, `"pt"`) |
| `flow` | `String?` | No | Flow to execute (e.g., `"home"`, `"activities"`, `"topup"`, `"cards"`) |
| `flowParams` | `Map<String, Any>?` | No | Additional flow parameters |
| `embedded` | `Boolean` | No | Embedded mode (default: `true`) |
| `logSetting` | `AstroLogSetting` | No | Logging configuration |

## Integration

### Jetpack Compose

```kotlin
import androidx.compose.runtime.*
import com.astropay.connect.core.AstroConfiguration
import com.astropay.connect.core.AstroResult
import com.astropay.connect.views.AstroConnectView

@Composable
fun MyScreen() {
    var showSDK by remember { mutableStateOf(false) }

    val configuration = remember {
        AstroConfiguration.builder()
            .setEnvironment("sandbox")
            .setAppIssuer("your-app-issuer")
            .setAccessToken("your-access-token")
            .build()
    }

    if (showSDK) {
        AstroConnectView(
            configuration = configuration,
            onResult = { result ->
                when (result) {
                    is AstroResult.Success -> {
                        println("Operation completed successfully")
                    }
                    is AstroResult.Failure -> {
                        println("Error: ${result.error.errorDetail}")
                        showSDK = false
                    }
                    is AstroResult.Closed -> {
                        println("User closed the SDK")
                        showSDK = false
                    }
                }
            }
        )
    } else {
        Button(onClick = { showSDK = true }) {
            Text("Open AstroPay")
        }
    }
}
```

### Custom Loading View

```kotlin
AstroConnectView(
    configuration = configuration,
    loadingContent = {
        Column(
            horizontalAlignment = Alignment.CenterHorizontally
        ) {
            CircularProgressIndicator()
            Spacer(modifier = Modifier.height(16.dp))
            Text("Loading...")
        }
    },
    onResult = { result -> /* Handle result */ }
)
```

## Handling Results

The SDK returns an `AstroResult` sealed class with three possible states:

```kotlin
sealed class AstroResult {
    data object Success : AstroResult()           // Operation completed successfully
    data class Failure(val error: AstroError)     // An error occurred
    data object Closed : AstroResult()            // User closed the SDK
}
```

### Using Result Extensions

```kotlin
result
    .onSuccess { println("Success!") }
    .onFailure { error -> println("Error: ${error.errorDetail}") }
    .onClosed { println("User closed SDK") }
```

## Error Codes

### Error Structure

```kotlin
val error: AstroError

error.errorCode        // Numeric code (e.g., "1003")
error.errorSubCode     // Optional subcode (e.g., "01")
error.errorMessage     // Descriptive message
error.errorDetail      // Full detail: "[1003-01] No internet connection"
```

### Error Table

| Code | Name | Description |
|------|------|-------------|
| `1001` | `INITIALIZATION_ERROR` | Error initializing the SDK |
| `1002` | `INVALID_CONFIG` | Invalid configuration |
| `1003` | `NETWORK_ERROR` | Network error |
| `1004` | `BRIDGE_ERROR` | Communication error with the app |
| `1005` | `TIMEOUT` | Request timed out |
| `1006` | `CAMERA_PERMISSION` | Camera permission error |

### Network Error Subcodes (1003)

| Subcode | Name | Description |
|---------|------|-------------|
| `01` | `NO_CONNECTION` | No internet connection |
| `02` | `HOST_NOT_FOUND` | Server not found |
| `03` | `TIMEOUT` | Connection timed out |
| `04` | `CANNOT_CONNECT` | Unable to connect to server |
| `05` | `CONNECTION_LOST` | Connection lost |
| `06` | `UNKNOWN` | Unknown network error |

### Bridge Error Subcodes (1004)

| Subcode | Name | Description |
|---------|------|-------------|
| `401` | `UNAUTHORIZED` | Authentication error (invalid or expired token) |

### Configuration Errors (1002)

| Message | Cause |
|---------|-------|
| `"accessToken is required"` | Empty access token |
| `"appIssuer is required"` | Empty app issuer |
| `"Environment is not supported"` | Invalid environment |

## Log Configuration

Logs are disabled in production for security.

```kotlin
val logSetting = AstroLogSetting(
    enabled = true,
    logLevel = AstroLogLevel.DEBUG  // ERROR, INFO, DEBUG
)

val configuration = AstroConfiguration.builder()
    .setEnvironment("sandbox")
    .setAppIssuer("your-app-issuer")
    .setAccessToken("your-access-token")
    .setLogSetting(logSetting)
    .build()
```

### Log Levels

| Level | Description |
|-------|-------------|
| `ERROR` | Errors only |
| `INFO` | Errors and general information |
| `DEBUG` | All messages including debug |

### Filtering Logs

You can filter logs by TAG: `AstroConnect`

```bash
adb logcat -s AstroConnect
```

## Environments

| Environment |
|-------------|
| `production` |
| `sandbox` |

## Support

For technical support, contact the AstroPay integrations team.
