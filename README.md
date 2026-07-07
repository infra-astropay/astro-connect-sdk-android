# AstroConnectSDK - Android

SDK for integrating AstroPay Connect into Android applications.

The SDK ships in **two flavors**: a **core** artifact (`com.astropay:connect`) for most integrations, and a **native KYC** artifact (`com.astropay:connect-native-kyc`) that adds on-device identity verification for regulated markets. Both expose the identical public API — see [Choosing an artifact](#choosing-an-artifact-core-vs-native-kyc) to pick one.

## Requirements

- Android SDK 24+ (Android 7.0)
- Kotlin 1.9+
- Jetpack Compose

> **Note:** The native KYC flow (available with the `com.astropay:connect-native-kyc` artifact) requires Android 8.0 (API 26) or higher. Devices running Android 7.x (API 24–25) will receive an error result from the KYC flow; all other SDK features remain fully functional.

## Installation

### Choosing an artifact: core vs. native KYC

The SDK ships **two artifacts** at the same version — the same integration code, but different bundled capabilities. Pick the one that matches your flows:

| Artifact | Use when | On-device identity verification | Extra Maven repository |
|----------|----------|---------------------------------|------------------------|
| `com.astropay:connect` (core) | The standard artifact for most integrations. Identity verification, when the server requests it, runs through the standard in-app browser flow. | Not included | None |
| `com.astropay:connect-native-kyc` (native KYC) | You operate in a regulated market (e.g. Brazil) that requires **on-device identity verification** — face liveness and document capture handled natively in your app rather than in the in-app browser. | Included | Requires the CAF repository (see below) |

**Not sure which to pick?** Use the core **`com.astropay:connect`** artifact — most integrations don't need native KYC, and switching to `com.astropay:connect-native-kyc` later requires **no code changes** (see [Switching between core and native KYC](#switching-between-core-and-native-kyc)).

Both artifacts expose the identical public API — only the bundled native KYC capability and the required Maven repositories differ. If you integrate `com.astropay:connect` and a native KYC flow is requested, it returns an error result instead of running, and every other feature works normally.

### Maven Repository

#### Core artifact (no native KYC)

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
    implementation("com.astropay:connect:1.0.16")
}
```

#### Native KYC artifact

The `connect-native-kyc` artifact additionally requires the CAF Maven repository. Add **both** repositories to your project's `settings.gradle.kts`:

```kotlin
dependencyResolutionManagement {
    repositories {
        google()
        mavenCentral()
        maven { url = uri("https://repo.combateafraude.com/android/release") }
        maven { url = uri("https://infra-astropay.github.io/astro-connect-sdk-android/") }
    }
}
```

Then add the dependency to your app's `build.gradle.kts`:

```kotlin
dependencies {
    implementation("com.astropay:connect-native-kyc:1.0.16")
}
```

> You don't declare any CAF dependency yourself — the CAF identity-verification libraries are pulled in transitively by `com.astropay:connect-native-kyc`. You only add the CAF Maven repository above so Gradle can resolve them.

> Use **either** `connect` **or** `connect-native-kyc`, not both. The CAF repository (`https://repo.combateafraude.com/android/release`) is only needed by `connect-native-kyc`; the core `connect` artifact resolves with the AstroPay repository alone.

### Switching between core and native KYC

Both artifacts expose an identical public API, so moving from `com.astropay:connect` to `com.astropay:connect-native-kyc` (or back) needs **no code changes**. To switch:

1. Change the dependency in your app's `build.gradle.kts` to `com.astropay:connect-native-kyc:<version>` (use the same version you had).
2. Add the CAF Maven repository to your `settings.gradle.kts` (see [Native KYC artifact](#native-kyc-artifact)):
   ```kotlin
   maven { url = uri("https://repo.combateafraude.com/android/release") }
   ```
3. No manifest changes are required — the `RECORD_AUDIO` permission used by the face liveness module merges into your app automatically.

> The native KYC flow requires Android 8.0 (API 26) or higher. On API 24–25 the KYC flow returns an error result; every other SDK feature keeps working.

To go back to core, reverse the steps: switch the dependency to `com.astropay:connect` and remove the CAF Maven repository from `settings.gradle.kts`.

## Configuration

### Required Permissions

Add the following permissions to your `AndroidManifest.xml` if the flow requires camera access:

```xml
<uses-permission android:name="android.permission.CAMERA" />
<uses-permission android:name="android.permission.INTERNET" />
```

> **Important:** The camera permission dialog will display your **app name** (from `android:label` in your `AndroidManifest.xml`) when requesting access. Make sure your app has a proper `android:label` configured so users see a recognizable name instead of the package name.

#### Native KYC — Additional Permission

> This applies only when you integrate the **`com.astropay:connect-native-kyc`** artifact. The core **`com.astropay:connect`** artifact does not run the native KYC flow and does not require microphone access.

If your integration uses the native KYC flow, the SDK also requires microphone access for the face liveness module. This permission is declared in the SDK's manifest and merges into your app automatically:

```xml
<uses-permission android:name="android.permission.RECORD_AUDIO" />
```

The SDK will request this permission at runtime alongside the camera permission when the KYC flow is triggered. No additional manifest changes are required in your app.

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
    .setFlowParams(mapOf("topup" to mapOf("amount" to 100))) // Flow parameters (optional)
    .setShowHeader(true)                      // Show header bar with close button (optional, default: true)
    .setShowHeaderLogo(true)                  // Show co-branded logo in header (optional, default: true)
    .setEmbedded(true)                        // Embedded mode (optional, default: true)
    .setBiometricGracePeriod(120)             // Seconds to skip biometric re-prompt (optional, default: 120)
    .setStyle(AstroStyle(                     // Custom style settings (optional)
        backgroundColor = Color.White.toArgb(),
        primaryColor = Color(0xFF00DBBF).toArgb(),
        buttons = AstroButtonStyle(
            colors = AstroButtonColors(
                primaryBackground = Color(0xFF00DBBF).toArgb(),
                primaryText = Color.Black.toArgb(),
            ),
        ),
        header = AstroHeaderStyle(
            backgroundColor = Color(0xFFEFEFEF).toArgb(),
            borderColor = Color(0xFFCCCCCC).toArgb(),
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
| `embedded` | `Boolean` | No | Embedded mode. Default: `true` |
| `biometricGracePeriod` | `Long?` | No | Seconds to skip biometric re-prompt after a successful auth. Default: `120` (2 min). Range: `0`–`600` (10 min). Set to `0` to always require biometric. |
| `style` | `AstroStyle?` | No | Custom style settings for background and header (see [Style Customization](#style-customization)) |
| `styleOverrides` | `Map<String, Any>?` | No | Free-form hex/color overrides forwarded to the web, mirroring the `AstroStyle` key shape. Escape hatch for tokens not yet in the typed catalog (see [Hex-string escape hatch via `styleOverrides`](#hex-string-escape-hatch-via-styleoverrides)) |
| `logSetting` | `AstroLogSetting` | No | Logging configuration |

### Home Banners

Banners are cross-cutting: you can pass them via `flowParams.banners` regardless of the active flow. The `bannerType` value determines where each banner is rendered, not the flow that was initialized. Two placements are supported:

- `home-page` — full-screen banner shown once per session before the home loads (e.g. onboarding).
- `home-header` — compact banner rendered at the top of the home. Multiple `home-header` banners scroll horizontally.

Each banner is a map inside the `banners` list.

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

### Topup Parameters

Topup parameters are cross-cutting: whenever the user lands on the topup amount screen — regardless of the flow that was initialized — you can preset the amount, the currency, and a list of suggested amounts that are rendered as pills below the amount input.

Pass these values under a nested `topup` map inside `flowParams`.

`currency` is required for `amount` and `suggestedAmounts` to take effect: if it is omitted, or does not match the currency shown on the screen, neither is applied.

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
            "topup" to mapOf(
                "amount" to 50,
                "currency" to "USD",
                "suggestedAmounts" to listOf(50, 100, 200),
            ),
        ),
    )
    .build()
```

For partners that operate multiple currencies, you can supply a per-currency preset map instead of (or alongside) the flat `suggestedAmounts` list. Keys are ISO 4217 codes (case-insensitive — they are normalized to uppercase internally):

```kotlin
// Per-currency preset map — overrides `suggestedAmounts` when present.
// Keys are ISO 4217 codes (case-insensitive).
val flowParams = mapOf(
    "topup" to mapOf(
        "suggestedAmountsByCurrency" to mapOf(
            "USD" to listOf(10, 25, 50, 100),
            "EUR" to listOf(10, 20, 50, 100),
            "BRL" to listOf(50, 100, 200, 500)
        )
    )
)
```

> **Precedence:** When both `suggestedAmounts` and `suggestedAmountsByCurrency` are provided, the per-currency map wins. If the user is on a currency that is not a key in the map, no preset pills are shown — the flat list is NOT consulted as a fallback.

> **Deprecated:** The flat keys `amount`, `currency`, and `suggestedAmounts` placed directly under `flowParams` are still accepted for backward compatibility, but the nested `flowParams.topup` shape is the recommended form. The flat keys will be removed in a future major version.

#### Topup Parameters

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `amount` | `Double` | No | Pre-fills the amount input. Requires `currency` and only applied when it matches the screen currency |
| `currency` | `String` | No | Target currency (ISO 4217 code, e.g., `"USD"`). Required for `amount` and `suggestedAmounts` to take effect |
| `suggestedAmounts` | `List<Double>` | No | List of positive amounts rendered as clickable pills below the input. Tapping a pill sets the input to that value. Requires `currency` and only rendered when it matches the screen currency. Ignored entirely when `suggestedAmountsByCurrency` is provided |
| `suggestedAmountsByCurrency` | `Map<String, List<Double>>` | No | Per-currency preset map. Keys are ISO 4217 codes (case-insensitive). When present, takes precedence over `suggestedAmounts`; if the screen currency is not a key in the map, no preset pills are shown |

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
                        when (result.code) {
                            "CLOSED_BY_USER_HEADER_BUTTON", "CLOSED_BY_USER_NAVIGATED_BACK", "CLOSED_BY_SYSTEM_DISMISS" -> {
                                // User backed out or pressed system back — log a dismissal event to your analytics
                                println("sdk_dismissed code=${result.code} message=${result.message}")
                            }
                            "CLOSED_BY_USER_SIGNED_OUT" -> {
                                // User signed out from inside the SDK — clear local session state
                                println("User signed out — clearing local session")
                            }
                            else -> {
                                println("SDK closed: ${result.code} — ${result.message}")
                            }
                        }
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

> **Custom header?** If your host renders its own header instead of the built-in one, drive the SDK with `AstroConnectController` and set `showHeader = false` so you can wire your own close button to `controller.close(...)`. See [Custom Header](#custom-header) for the full setup.

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
    data object Success : AstroResult()                                              // Operation completed successfully
    data class Failure(val error: AstroError) : AstroResult()                        // An error occurred
    data class Closed(val code: String, val message: String) : AstroResult()         // User closed the SDK
    data class Event(val event: AstroEvent) : AstroResult()                          // An analytics event was received
}
```

```kotlin
onResult = { result ->
    when (result) {
        is AstroResult.Success -> println("Success")
        is AstroResult.Failure -> println("Error: ${result.error.errorDetail}")
        is AstroResult.Closed -> println("Closed: ${result.code} — ${result.message}")
        is AstroResult.Event -> {
            println("Event: ${result.event.eventName} - ${result.event.eventCategory}")
            // Send to your analytics platform
        }
    }
}
```

### Close payload

The `code` and `message` properties of `AstroResult.Closed` describe why the SDK closed:

- `code: String` — a short machine-readable identifier in `UPPER_SNAKE_CASE`. All codes follow the `CLOSED_BY_*` convention (e.g. `CLOSED_BY_USER_HEADER_BUTTON`, `CLOSED_BY_HOST_APP`) and describe who or what triggered the close. Branch on this value when you need different behavior per close source.
- `message: String` — a human-readable description, useful for logging.

Both are plain strings. There is no enum or whitelist on the integrator side; the SDK fixes the values it emits and may introduce additional `code` values for in-SDK close paths in future releases without breaking the API. Treat any unrecognized `code` as a generic close.

| code | source | typical message |
|------|--------|-----------------|
| `CLOSED_BY_USER_HEADER_BUTTON` | The built-in header close button | `User tapped the close button` |
| `CLOSED_BY_HOST_APP` | `AstroConnectController.close()` | `Closed by host integrator` |
| `CLOSED_BY_SYSTEM_DISMISS` | SDK view was dismissed without an explicit close call (system back-press, sheet swipe-down, navigation back-swipe, host removal) | `View was dismissed` |
| `UNKNOWN` | Fallback when the close payload is missing or malformed | `` (empty string) |
| `CLOSED_BY_USER_NAVIGATED_BACK` | User backed out at the root of a flow sub-tree | descriptive (e.g., `User backed out of activities`) |
| `CLOSED_BY_USER_DISMISSED_ERROR` | User dismissed a terminal-error screen | descriptive (e.g., `User dismissed biometric error`) |
| `CLOSED_BY_USER_CANCELLED_PIN` | User cancelled the PIN re-prompt | `User cancelled PIN re-prompt` |
| `CLOSED_BY_USER_SIGNED_OUT` | User signed out from inside the SDK | `User signed out` |

The `CLOSED_BY_USER_NAVIGATED_BACK`, `CLOSED_BY_USER_DISMISSED_ERROR`, `CLOSED_BY_USER_CANCELLED_PIN`, and `CLOSED_BY_USER_SIGNED_OUT` entries above are common examples — the list of in-SDK codes is not exhaustive.

### Using Result Extensions

```kotlin
result
    .onSuccess { println("Success!") }
    .onFailure { error -> println("Error: ${error.errorDetail}") }
    .onClosed { code, message -> println("User closed SDK: $code — $message") }
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

| Property          | Type                   | Description                                                                                                            |
|-------------------|------------------------|------------------------------------------------------------------------------------------------------------------------|
| `backgroundColor` | `@ColorInt Int?`       | Main background color (e.g., `Color.White.toArgb()`). Also cascades to `surface.base` when not overridden.            |
| `primaryColor`    | `@ColorInt Int?`       | Primary brand color. Cascades to `surface.highlight`, `text.highlight`, and `border.highlight` when those tokens are not overridden. |
| `surface`         | `AstroSurfaceColors?`  | Background fills for containers, cards, banners and overlays. See [Style Tokens Reference](STYLE-TOKENS.md#astrosurfacecolors).           |
| `text`            | `AstroTextColors?`     | Foreground colors for typography. See [Style Tokens Reference](STYLE-TOKENS.md#astrotextcolors).                                       |
| `border`          | `AstroBorderColors?`   | Stroke colors for outlines, dividers, and separators. See [Style Tokens Reference](STYLE-TOKENS.md#astrobordercolors).                   |
| `typography`      | `AstroTypography?`           | Global typography settings — single field `fontFamily: String?` used as the default font family across the SDK. See [Style Tokens Reference](STYLE-TOKENS.md#astrotypography). |
| `buttons`         | `AstroButtonStyle?`          | Wrapper around `AstroButtonColors` (12 variants × 11 props) and optional `AstroButtonTypography`. See [Style Tokens Reference](STYLE-TOKENS.md#astrobuttonstyle). |
| `buttonsIcon`     | `AstroButtonIconStyle?`      | Wrapper around `AstroButtonIconColors` (same 12 variants, icon-specific) and optional `AstroButtonIconTypography`. See [Style Tokens Reference](STYLE-TOKENS.md#astrobuttoniconstyle). |
| `buttonsPill`     | `AstroButtonPillStyle?`      | Wrapper around `AstroButtonPillColors` (14 statuses) and optional `AstroButtonPillTypography`. See [Style Tokens Reference](STYLE-TOKENS.md#astrobuttonpillstyle). |
| `inputs`          | `AstroInputStyle?`           | Wrapper around `AstroInputColors` and optional `AstroInputTypography` (input / label / helper / placeholder). See [Style Tokens Reference](STYLE-TOKENS.md#astroinputstyle). |
| `header`          | `AstroHeaderStyle?`    | Header style settings                                                                                                  |

> All color values are `@ColorInt Int?` — construct them with `Color(0xFF…).toArgb()`, `ContextCompat.getColor(context, R.color.x)`, or any standard Android color literal. Alpha is honored: an `@ColorInt` with an alpha channel below `0xFF` (e.g. `0x80FF0000` for 50%-opacity red) is delivered to the SDK and rendered with transparency. See the [Style Tokens Reference — Color values](STYLE-TOKENS.md#color-values) for details.

### AstroHeaderStyle

| Property            | Type             | Default     | Description                                                      |
|---------------------|------------------|-------------|------------------------------------------------------------------|
| `backgroundColor`   | `@ColorInt Int?` | Theme-based | Header background color                                          |
| `borderColor`       | `@ColorInt Int?` | Theme-based | Header bottom border color                                       |
| `borderWidth`       | `Float?`  | `2f`        | Header bottom border width in dp. Set to `0f` to hide the border |
| `paddingHorizontal` | `Float?`  | `8f`        | Horizontal padding inside the header in dp                       |
| `paddingVertical`   | `Float?`  | `8f`        | Vertical padding inside the header in dp                         |

### Example

```kotlin
val style = AstroStyle(
    backgroundColor = Color.White.toArgb(),
    primaryColor = Color(0xFF00DBBF).toArgb(),
    buttons = AstroButtonStyle(
        colors = AstroButtonColors(
            primaryBackground = Color(0xFF00DBBF).toArgb(),
            primaryText = Color.Black.toArgb(),
        ),
    ),
    surface = AstroSurfaceColors(
        // 50%-opacity black scrim — alpha is honored end-to-end.
        overlay = Color(0x80000000).toArgb(),
    ),
    header = AstroHeaderStyle(
        backgroundColor = Color(0xFFEFEFEF).toArgb(),
        borderColor = Color(0xFFCCCCCC).toArgb(),
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
>     backgroundColor = if (isDark) Color(0xFF041311).toArgb() else Color.White.toArgb(),
>     header = AstroHeaderStyle(
>         backgroundColor = if (isDark) Color(0xFF061E1D).toArgb() else Color(0xFFEFEFEF).toArgb()
>     )
> )
> ```

### Hex-string escape hatch via `styleOverrides`

For partners who need to pass colors as hex strings — for example, theme values fetched from a remote configuration service, or tokens not yet exposed by the typed `AstroStyle` catalog — use `AstroConfiguration.Builder.setStyleOverrides(...)`. Its color leaves accept both hex strings (`"#RRGGBB"` / `"#RRGGBBAA"`) and native `androidx.compose.ui.graphics.Color` values interchangeably. See [Free-form overrides via `styleOverrides`](STYLE-TOKENS.md#free-form-overrides-via-styleoverrides) for the full contract.

```kotlin
val configuration = AstroConfiguration.builder()
    .setEnvironment("sandbox")
    .setAppIssuer("your-app-issuer")
    .setClientId("your-client-id")
    .setPartnerUserId("your-partner-user-id")
    .setAccessToken("your-access-token")
    .setStyleOverrides(
        mapOf(
            "backgroundColor" to "#FFFFFF",
            "primaryColor" to "#00DBBF",
        )
    )
    .build()
```

> **Native KYC:** The native identity verification flow (available with the **`com.astropay:connect-native-kyc`** artifact) also honors your `AstroConfiguration`. Your `style` / `styleOverrides` colors theme its screens — background, primary/brand color, on-screen text, and borders all follow your configured palette — and `theme` (`LIGHT` / `DARK` / `SYSTEM`) sets light or dark mode. The native KYC screens render in the **device language**, not the SDK's `language` config — unlike the web flow, where `language` applies. An integration that sets no colors is unchanged.

## Co-Branded Header Logo

When `showHeaderLogo` is `true` (the default), the SDK header displays a co-branded logo for the issuer. The logo is fetched automatically based on the `appIssuer` value and the current theme.

- The SDK looks for a logo at: `{baseUrl}/{appIssuer}_{theme}.webp`
- If the issuer logo is not found, it falls back to the default AstroPay logo
- Set `showHeaderLogo` to `false` to hide the logo entirely

## Custom Header

If you set `showHeader` to `false` and render your own header (for example, a custom close button or a top app bar), use `AstroConnectController` to dismiss the SDK from your own UI. Calling `controller.close()` funnels into the same close path as the built-in header button and emits `AstroResult.Closed` exactly once.

```kotlin
import androidx.compose.foundation.layout.*
import androidx.compose.material3.*
import androidx.compose.runtime.*
import androidx.compose.ui.Modifier
import com.astropay.connect.AstroConnectController
import com.astropay.connect.core.AstroConfiguration
import com.astropay.connect.core.AstroResult
import com.astropay.connect.views.AstroConnectView

@Composable
fun CustomHeaderScreen() {
    var showSDK by remember { mutableStateOf(false) }
    val controller = remember { AstroConnectController() }

    val configuration = remember {
        AstroConfiguration.builder()
            .setEnvironment("sandbox")
            .setAppIssuer("your-app-issuer")
            .setClientId("your-client-id")
            .setPartnerUserId("your-partner-user-id")
            .setAccessToken("your-access-token")
            .setShowHeader(false)
            .build()
    }

    if (showSDK) {
        Column(modifier = Modifier.fillMaxSize()) {
            // Your custom header
            Row(
                modifier = Modifier.fillMaxWidth().padding(16.dp),
                verticalAlignment = Alignment.CenterVertically,
            ) {
                Text("My App", style = MaterialTheme.typography.titleMedium)
                Spacer(modifier = Modifier.weight(1f))
                TextButton(onClick = { controller.close() }) {
                    Text("Close")
                }
            }

            AstroConnectView(
                configuration = configuration,
                controller = controller,
                onResult = { result ->
                    if (result is AstroResult.Closed) {
                        showSDK = false
                    }
                },
            )
        }
    } else {
        Button(onClick = { showSDK = true }) {
            Text("Open AstroPay")
        }
    }
}
```

> `controller.close()` is idempotent — subsequent calls after the SDK has already closed are no-ops. The same applies if the SDK closes itself first (for example, when the user completes the flow): a later `controller.close()` will not re-fire `AstroResult.Closed`. `close()` is safe to call from any thread.

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
- [Style Tokens Reference](STYLE-TOKENS.md) — Full catalog of design tokens accepted by `AstroStyle` (surface, text, border, buttons), plus cascade rules.
- [Migration Guides](migrations/) — Step-by-step guides for upgrading between versions that include breaking changes.

## Support

For technical support, contact the AstroPay integrations team.
