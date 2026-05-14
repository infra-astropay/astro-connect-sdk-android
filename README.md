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
    implementation("com.astropay:connect:1.0.13")
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

> **Important:** The camera permission dialog will display your **app name** (from `android:label` in your `AndroidManifest.xml`) when requesting access. Make sure your app has a proper `android:label` configured so users see a recognizable name instead of the package name.

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
    .setClientId("your-client-id")            // Client identifier (required)
    .setPartnerUserId("your-partner-user-id") // Partner user identifier (required)
    .setPhoneCode("51")                       // Phone country code (optional)
    .setPhoneNumber("123456789")              // Phone number (optional)
    .setAccessToken("your-access-token")      // Authentication token
    .setTheme(AstroTheme.SYSTEM)              // LIGHT, DARK, SYSTEM (optional)
    .setLanguage("en")                        // Language code (optional, default: "en")
    .setFlow("home")                          // Specific flow (optional)
    .setFlowParams(mapOf("amount" to 100))    // Flow parameters (optional)
    .setShowHeader(true)                      // Show header bar with close button (optional, default: true)
    .setShowHeaderLogo(true)                  // Show co-branded logo in header (optional, default: true)
    .setEmbedded(true)                        // Embedded mode (optional, default: true)
    .setBiometricGracePeriod(120)             // Seconds to skip biometric re-prompt (optional, default: 120)
    .setStyle(AstroStyle(                     // Custom style settings (optional)
        backgroundColor = "#FFFFFF",
        header = AstroHeaderStyle(
            backgroundColor = "#EFEFEF",
            borderColor = "#CCCCCC",
            borderWidth = 1f,
            paddingHorizontal = 8f,
            paddingVertical = 8f
        )
    ))
    .setLogSetting(AstroLogSetting(           // Log configuration (optional)
        enabled = true,
        logLevel = AstroLogLevel.DEBUG
    ))
    .build()

// Or using the constructor directly
val configuration = AstroConfiguration(
    environment = "sandbox",
    appIssuer = "your-app-issuer",
    clientId = "your-client-id",
    partnerUserId = "your-partner-user-id",
    phoneCode = "51",
    phoneNumber = "123456789",
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
| `clientId` | `String` | Yes | Client identifier |
| `partnerUserId` | `String` | Yes | Partner user identifier |
| `phoneCode` | `String?` | No | Phone country code (e.g., `"51"`, `"54"`) |
| `phoneNumber` | `String?` | No | Phone number |
| `accessToken` | `String` | No | Authentication token. |
| `theme` | `AstroTheme` | No | Visual theme: `LIGHT`, `DARK`, `SYSTEM`. Default: `SYSTEM` |
| `language` | `String` | No | Language code (e.g., `"en"`, `"es"`, `"pt"`). Default: `"en"` |
| `flow` | `String?` | No | Flow to execute (e.g., `"home"`, `"activities"`, `"topup"`, `"cards"`) |
| `flowParams` | `Map<String, Any>?` | No | Additional flow parameters |
| `showHeader` | `Boolean?` | No | Show header bar with close button and co-branded logo (default: `true`) |
| `showHeaderLogo` | `Boolean?` | No | Show co-branded issuer logo in the header (default: `true`) |
| `showCloseButton` | `Boolean?` | No | **Deprecated.** Use `showHeader` instead. |
| `autoSize` | `Boolean?` | No | **Deprecated.** Use `showHeader` instead. |
| `embedded` | `Boolean` | No | Embedded mode. Default: `true` |
| `biometricGracePeriod` | `Long?` | No | Seconds to skip biometric re-prompt after a successful auth. Default: `120` (2 min). Range: `0`–`600` (10 min). Set to `0` to always require biometric. |
| `style` | `AstroStyle?` | No | Custom style settings for background and header (see [Style Customization](#style-customization)) |
| `logSetting` | `AstroLogSetting` | No | Logging configuration |

### Home Banners

When the SDK lands on the home screen (either `setFlow("home")` or when no flow is specified), you can render promotional banners by passing a `banners` list inside `flowParams`. Each banner is a map and supports two types:

- `home-page` — full-screen banner shown once per session before the home loads (e.g. onboarding).
- `home-header` — compact banner rendered at the top of the home. Multiple `home-header` banners scroll horizontally.

```kotlin
val configuration = AstroConfiguration.builder()
    .setEnvironment("sandbox")
    .setAppIssuer("your-app-issuer")
    .setClientId("your-client-id")
    .setPartnerUserId("your-partner-user-id")
    .setAccessToken("your-access-token")
    .setFlow("home")
    .setFlowParams(
        mapOf(
            "banners" to listOf(
                mapOf(
                    "bannerType" to "home-page",
                    "bannerTitle" to "Your wallet is ready to use!",
                    "bannerDescription" to "Top up now and get 5% cashback on your first transaction.",
                    "bannerActionText" to "Top Up Now",
                    "bannerDismissText" to "Dismiss",
                    "bannerDeepLink" to "topup",
                    "bannerImage" to "banner-home-page-en",
                    "bannerImageSize" to "30vh",
                ),
                mapOf(
                    "bannerType" to "home-header",
                    "bannerTitle" to "Your wallet is ready to use!",
                    "bannerDescription" to "Top up now and get 5% cashback.",
                    "bannerActionText" to "Top Up Now",
                    "bannerDeepLink" to "topup",
                    "bannerImage" to "banner-home-header-en",
                ),
            ),
        ),
    )
    .build()
```

#### Banner Parameters

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `bannerType` | `String` | Yes | Banner placement: `"home-page"` or `"home-header"` |
| `bannerTitle` | `String?` | No | Title text |
| `bannerDescription` | `String?` | No | Description text |
| `bannerActionText` | `String?` | No | Primary action button label. If omitted on a `home-header` banner, a chevron is shown instead and the entire banner is clickable |
| `bannerDismissText` | `String?` | No | Dismiss button label (only used by `home-page`) |
| `bannerDeepLink` | `String` | Yes | Deep link triggered on action: `"topup"`, `"activities"`, `"cards"`, `"withdrawal"` |
| `bannerImage` | `String?` | No | Image asset name to render on the banner |
| `bannerImageSize` | `String?` | No | Image size for `home-page` banners. Accepts a CSS length in `px`, `vh`, or `vw` (e.g. `"200px"`, `"50vh"`, `"40vw"`). Defaults to `30vh` when omitted or invalid |

### Topup Flow Parameters

When the SDK opens in the topup flow (`setFlow("topup")`), you can preset the amount, the currency, and a list of suggested amounts that are rendered as pills below the amount input.

```kotlin
val configuration = AstroConfiguration.builder()
    .setEnvironment("sandbox")
    .setAppIssuer("your-app-issuer")
    .setClientId("your-client-id")
    .setPartnerUserId("your-partner-user-id")
    .setAccessToken("your-access-token")
    .setFlow("topup")
    .setFlowParams(
        mapOf(
            "amount" to 50,
            "currency" to "USD",
            "suggestedAmounts" to listOf(50, 100, 200),
        ),
    )
    .build()
```

#### Topup Parameters

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `amount` | `Double` | No | Pre-fills the amount input |
| `currency` | `String` | No | Pre-selects the currency (ISO 4217 code, e.g., `"USD"`) |
| `suggestedAmounts` | `List<Double>` | No | List of positive amounts rendered as clickable pills below the input. Tapping a pill sets the input to that value |

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
            .setClientId("your-client-id")
            .setPartnerUserId("your-partner-user-id")
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
                    is AstroResult.Event -> {
                        println("Event: ${result.event.eventName}")
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

## Performance Optimization

### Pre-Warming (Recommended)

Initializes the SDK in the background as early as possible (e.g. `Application.onCreate` or `MainActivity.onCreate`). This reduces the cold-start delay so the first SDK open feels instant.

If `appIssuer` is provided, the co-branded header logo is also prepared in advance.

```kotlin
AstroConnect.preWarm(
    environment = "sandbox",
    appIssuer = "your-app-issuer",   // Optional — prepares the header logo in advance
    onSuccess = { println("SDK ready") },
    onError = { error -> println("Pre-warm failed: ${error.errorDetail}") }
)
```

#### Parameters

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `environment` | `String` | Yes | Target environment: `"production"`, `"sandbox"` |
| `appIssuer` | `String?` | No | If provided, the co-branded header logo is prepared in advance |
| `logSetting` | `AstroLogSetting?` | No | Logging configuration |
| `force` | `Boolean` | No | Force re-initialization even if already completed (default: `false`) |
| `onSuccess` | `(() -> Unit)?` | No | Called when the SDK is ready |
| `onError` | `((AstroError) -> Unit)?` | No | Called if initialization fails |

### Pre-Loading

Pre-loads the SDK with a specific configuration before presenting it to the user. Call this when the user lands on a screen that will open the SDK shortly. When `AstroConnectView` is composed with the same configuration, it appears instantly with no loading screen.

> **Important:** A pre-load is **single-use** and **configuration-bound**.
> - Once `AstroConnectView` is composed, the pre-loaded session is consumed. To keep the instant-open behavior the next time the user enters the SDK, call `preload` again after the view leaves composition.
> - If the configuration passed to `AstroConnectView` differs from the one used in `preload`, the pre-load is discarded and the SDK initializes normally. If the configuration may change after pre-loading, call `preload` again with the updated configuration to restore the fast path.
> - When biometric authentication is required for the user, any prompt is automatically deferred until `AstroConnectView` is displayed, so the user is never prompted before the SDK is on screen.

```kotlin
AstroConnect.preload(
    configuration = configuration,
    onPreloadEnded = { reason ->
        when (reason) {
            is AstroPreloadEndReason.Loaded -> println("SDK ready — will open instantly")
            is AstroPreloadEndReason.Deferred -> println("Preload deferred — will resume when AstroConnectView is displayed")
            is AstroPreloadEndReason.Failed -> println("Preload failed: ${reason.error.errorDetail}")
        }
    },
)
```

#### Parameters

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `configuration` | `AstroConfiguration` | Yes | The same configuration that will be passed to `AstroConnectView` |
| `onPreloadEnded` | `((AstroPreloadEndReason) -> Unit)?` | No | Called once with the reason the preload phase ended |

#### `AstroPreloadEndReason`

| Case | Meaning |
|------|---------|
| `Loaded` | The page finished loading during preload. The next `AstroConnectView` opens instantly. |
| `Deferred` | The preload ended before the page fully loaded because the SDK deferred a biometric prompt or the integrator displayed `AstroConnectView` before loading finished. The remaining work continues on the live view. |
| `Failed(error)` | The preload failed (network error, timeout, invalid configuration). |

> The previous `onSuccess` / `onError` overload is deprecated but still works for backward compatibility.

### Clearing SDK Data

Resets the SDK to a clean state. Also discards any active pre-load. Call this after user logout or when a fresh start is required.

```kotlin
AstroConnect.clear(
    environment = "sandbox",
    onSuccess = { println("SDK data cleared") },
    onError = { error -> println("Clear failed: ${error.errorDetail}") }
)
```

#### Parameters

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `environment` | `String` | Yes | Target environment: `"production"`, `"sandbox"` |
| `onSuccess` | `(() -> Unit)?` | No | Called when the SDK data has been cleared |
| `onError` | `((AstroError) -> Unit)?` | No | Called if clearing fails unexpectedly |

## Handling Results

The SDK returns an `AstroResult` sealed class with four possible states:

```kotlin
sealed class AstroResult {
    data object Success : AstroResult()           // Operation completed successfully
    data class Failure(val error: AstroError)     // An error occurred
    data object Closed : AstroResult()            // User closed the SDK
    data class Event(val event: AstroEvent)       // An analytics event was received
}
```

```kotlin
onResult = { result ->
    when (result) {
        is AstroResult.Success -> println("Success")
        is AstroResult.Failure -> println("Error: ${result.error.errorDetail}")
        is AstroResult.Closed -> println("Closed")
        is AstroResult.Event -> {
            println("Event: ${result.event.eventName} - ${result.event.eventCategory}")
            // Send to your analytics platform
        }
    }
}
```

### Using Result Extensions

```kotlin
result
    .onSuccess { println("Success!") }
    .onFailure { error -> println("Error: ${error.errorDetail}") }
    .onClosed { println("User closed SDK") }
    .onEvent { event -> println("Event: ${event.eventName}") }
```

## Events

The SDK emits analytics events during user interactions via `AstroResult.Event`. Each event exposes the following fields:

### Event Structure

```kotlin
data class AstroEvent(
    val screenName: String,              // Screen where the event occurred
    val eventName: String,               // Name of the event
    val eventCategory: String,           // Category: "user_action", "page_view", etc.
    val eventProperties: Map<String, Any>?,  // Additional event data (optional)
    val sessionId: String,               // Session identifier
    val appVersion: String,              // SDK version
    val platform: String                 // Platform: "android"
)
```

### Accessing Event Properties

```kotlin
is AstroResult.Event -> {
    // Access a specific property safely
    val amount = result.event.eventProperties?.get("amount") as? Int
    if (amount != null) {
        println("Amount: $amount")
    }
}
```
For the full catalog of events, screen names, and their properties, see [Events Reference](EVENTS.md).

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
| `01`  | `JSON_PARSING_ERROR` | Error parsing data from the SDK |
| `401` | `UNAUTHORIZED` | Authentication error (invalid or expired token) |

### Configuration Errors (1002)

| Message | Cause |
|---------|-------|
| `"appIssuer is required"` | Empty app issuer |
| `"clientId is required"` | Empty client ID |
| `"partnerUserId is required"` | Empty partner user ID |
| `"Environment is not supported"` | Invalid environment |
| `"biometricGracePeriod must be between 0 and 600 seconds"` | `biometricGracePeriod` outside the supported range |

## Log Configuration

Logs are disabled in production for security.

```kotlin
val logSetting = AstroLogSetting(
    enabled = true,
    verbose = false,                // Verbose mode (optional, default: false)
    logLevel = AstroLogLevel.DEBUG  // ERROR, INFO, DEBUG
)

val configuration = AstroConfiguration.builder()
    .setEnvironment("sandbox")
    .setAppIssuer("your-app-issuer")
    .setClientId("your-client-id")
    .setPartnerUserId("your-partner-user-id")
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

## Style Customization

You can customize the SDK's visual appearance using `AstroStyle`. This allows you to override the default background color and header styling.

### AstroStyle

| Property          | Type                 | Description                                             |
|-------------------|----------------------|---------------------------------------------------------|
| `backgroundColor` | `String?`            | Main background color as hex string (e.g., `"#FFFFFF"`) |
| `header`          | `AstroHeaderStyle?`  | Header style settings                                   |

### AstroHeaderStyle

| Property            | Type      | Default     | Description                                                      |
|---------------------|-----------|-------------|------------------------------------------------------------------|
| `backgroundColor`   | `String?` | Theme-based | Header background color as hex string                            |
| `borderColor`       | `String?` | Theme-based | Header bottom border color as hex string                         |
| `borderWidth`       | `Float?`  | `2f`        | Header bottom border width in dp. Set to `0f` to hide the border |
| `paddingHorizontal` | `Float?`  | `8f`        | Horizontal padding inside the header in dp                       |
| `paddingVertical`   | `Float?`  | `8f`        | Vertical padding inside the header in dp                         |

### Example

```kotlin
val style = AstroStyle(
    backgroundColor = "#FFFFFF",
    header = AstroHeaderStyle(
        backgroundColor = "#EFEFEF",
        borderColor = "#CCCCCC",
        borderWidth = 1f,
        paddingHorizontal = 8f,
        paddingVertical = 8f
    )
)

val configuration = AstroConfiguration.builder()
    .setEnvironment("sandbox")
    .setAppIssuer("your-app-issuer")
    .setClientId("your-client-id")
    .setPartnerUserId("your-partner-user-id")
    .setAccessToken("your-access-token")
    .setStyle(style)
    .build()
```

> **Note:** When `style` is not provided, the SDK uses default colors based on the selected theme (light/dark/system).
>
> **Important:** When you override a color via `AstroStyle`, the SDK **stops using the theme-based default** for that property. This means you are responsible for providing the appropriate value for both light and dark modes. For example:
>
> ```kotlin
> val isDark = (context.resources.configuration.uiMode
>     and android.content.res.Configuration.UI_MODE_NIGHT_MASK)  ==
>     android.content.res.Configuration.UI_MODE_NIGHT_YES
>
> val style = AstroStyle(
>     backgroundColor = if (isDark) "#041311" else "#FFFFFF",
>     header = AstroHeaderStyle(
>         backgroundColor = if (isDark) "#061E1D" else "#EFEFEF"
>     )
> )
> ```

## Co-Branded Header Logo

When `showHeaderLogo` is `true` (the default), the SDK header displays a co-branded logo for the issuer. The logo is fetched automatically based on the `appIssuer` value and the current theme.

- The SDK looks for a logo at: `{baseUrl}/{appIssuer}_{theme}.webp`
- If the issuer logo is not found, it falls back to the default AstroPay logo
- Set `showHeaderLogo` to `false` to hide the logo entirely

## Custom Loading View

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

## Environments

| Environment |
|-------------|
| `production` |
| `sandbox` |

## Resources

- [Changelog](CHANGELOG.md) — Version history and what changed in each release.
- [Events Reference](EVENTS.md) — All analytics events emitted by the SDK, including screen names, event names, categories, and properties.
- [Migration Guides](migrations/) — Step-by-step guides for upgrading between versions that include breaking changes.

## Support

For technical support, contact the AstroPay integrations team.
