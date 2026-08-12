# Changelog

All notable changes to the AstroConnectSDK for Android will be documented in this file.

---
## [1.0.17]

### Added

- **QR Scanner**: the SDK now presents its own built-in QR scanner when the user taps "Scan QR Code" in the PIX Send Money flow. No configuration is required from the host app. The `android.permission.CAMERA` permission must be declared in your `AndroidManifest.xml` (already required for identity verification).
- **External banner links**: a home banner can now send the user outside the SDK — to a website or to a screen in your own app — by setting `bannerLinkTarget` to `"external"`. Purely additive: existing banner payloads keep their current behavior. See [Banner Link Target](README.md#banner-link-target) in the README.
- **Dismissible header banners**: a `home-header` banner can now show a close button while still being tappable, by setting `bannerDismissible` to `true`. Previously a banner could be either dismissible or tappable, not both. Purely additive: existing banner payloads keep their current behavior. See [Dismissing a header banner](README.md#dismissing-a-header-banner) in the README.
- **Avatar colors**: `AstroStyle` now exposes an `avatar` slot for theming user avatars and avatar groups. Purely additive — existing configurations are unaffected. See [Style Tokens Reference](STYLE-TOKENS.md#astroavatarcolors).
- **Back button colors**: `AstroStyle.buttonsIcon` now exposes two icon-only back-button variants (`backDefault*` and `backTransparent*`, 22 tokens) so the back button can be themed independently from the gray and transparent icon buttons. Purely additive and color-neutral on upgrade — until you set them, the back button keeps exactly the colors it has today. See [Style Tokens Reference](STYLE-TOKENS.md#astrobuttoniconstyle).

---

## [1.0.16]

### Added

- **Native KYC** (`com.astropay:connect-native-kyc` artifact): the SDK now runs the identity verification flow natively on the device when the server enables it, instead of falling back to an in-app browser session. Native KYC ships in the new `com.astropay:connect-native-kyc` artifact; the core `com.astropay:connect` artifact is unchanged for everyone else and includes no identity-verification dependency, so it needs no extra Maven repository. The native flow honors your configured `style` / `styleOverrides`, `theme`, and `language`. Choose the artifact that matches your flows — see [Installation](README.md#installation).
- When using `com.astropay:connect-native-kyc`, the SDK's manifest declares `android.permission.RECORD_AUDIO`. This permission merges into your app automatically and is required by the face liveness module. The SDK requests it at runtime alongside the camera permission when the KYC flow is triggered — no changes to your `AndroidManifest.xml` are needed. The core `com.astropay:connect` artifact does not declare this permission.
- **Native KYC progress events**: the native identity verification flow now reports its progress on the SDK's event callback (`AstroResult.Event`), emitting a loading event when the flow starts and further events when a document result and a liveness result are produced. Purely additive — no action required.

---

## [1.0.15]

> **Breaking change:** The color fields on the typed `AstroStyle` API changed from hex `String?` to `@ColorInt Int?`. This affects `AstroStyle.backgroundColor`, `AstroHeaderStyle.backgroundColor`, and `AstroHeaderStyle.borderColor`. Code that passes a hex string (e.g. `AstroStyle(backgroundColor = "#FFFFFF")`) will no longer compile — pass a native color instead, or move the hex value into the `styleOverrides` map. See the [Migration Guide](migrations/v1.0.14-to-v1.0.15.md) for details.

### Added

- **Style customization**: `AstroStyle` now lets you override colors and typography across the SDK, including brand colors, surface/text/border tokens, per-component wrappers (`buttons`, `buttonsIcon`, `buttonsPill`, `inputs`), and a global font family. See [Style Customization](README.md#style-customization) and the [Style Tokens Reference](STYLE-TOKENS.md) for the full catalog.
  - Color leaves in `styleOverrides` accept both hex strings (6- or 8-digit `#RRGGBB` / `#RRGGBBAA`) and `androidx.compose.ui.graphics.Color` values.
  - Brand colors and the typed `surface.base` / `surface.highlight` sub-tokens apply throughout the SDK, including the initial loading screen and spinner. See [Brand color aliases](STYLE-TOKENS.md#brand-color-aliases-apply-throughout-the-sdk).

---

## [1.0.14]

> **Breaking change:** `AstroResult.Closed` changed from a `data object` to a `data class` carrying `code: String` and `message: String`. Any `when (result)` over `AstroResult` must update the `Closed` arm, and `result === AstroResult.Closed` referential checks no longer compile. The `AstroResult.onClosed { }` helper block now receives `(code, message)` as parameters. See the [Migration Guide](migrations/v1.0.13-to-v1.0.14.md) for details.

> **Breaking change:** The previously deprecated `AstroConfiguration` parameters `showCloseButton` / `autoSize` and the matching `Builder` setters `setShowCloseButton` / `setAutoSize` have been removed. Code that still references them will fail to compile. Use `showHeader` (and `Builder.setShowHeader`) instead. See the [Migration Guide](migrations/v1.0.13-to-v1.0.14.md) for details.

### Added

- **Custom header support**: new `AstroConnectController` with a public `close()` method for integrators who set `showHeader` to `false` and render their own header. Pass the controller into `AstroConnectView(configuration, controller, onResult)` and call `controller.close()` from your custom close button to dismiss the SDK. See [Custom Header](README.md#custom-header) in the README.
- **Close payload**: `AstroResult.Closed` now delivers `code: String` and `message: String`. `code` is a short machine-readable identifier (UPPER_SNAKE_CASE) and `message` is a human-readable description. Lets you distinguish whether the SDK was closed by the built-in header button (`CLOSED_BY_USER_HEADER_BUTTON`), by your own `controller.close()` call (`CLOSED_BY_HOST_APP`), by the SDK view being dismissed without an explicit close call (`CLOSED_BY_SYSTEM_DISMISS`), or by a flow-specific path inside the SDK (for example `CLOSED_BY_USER_NAVIGATED_BACK`, `CLOSED_BY_USER_SIGNED_OUT`, `CLOSED_BY_USER_CANCELLED_PIN`, `CLOSED_BY_USER_DISMISSED_ERROR`). See [Close payload](README.md#close-payload) in the README.
- **System dismissal handling**: when the SDK view is dismissed without an explicit close call — system back-press, sheet swipe-down, navigation back-swipe, host removal, or any other path that removes the view from the screen — the SDK surfaces `AstroResult.Closed(code = "CLOSED_BY_SYSTEM_DISMISS", message = "View was dismissed")` exactly once. Once any close path has fired, subsequent back-presses propagate to the parent normally.
- Added support for `flowParams.topup.suggestedAmountsByCurrency` — a per-currency preset map. Overrides `suggestedAmounts` when present. No SDK changes required; just add the key to your dictionary.

### Changed

- **Topup parameters**: `amount` and `suggestedAmounts` now apply whenever the user lands on the topup amount screen, regardless of the active flow. Both now require `currency` to be provided in `flowParams` and to match the screen currency; otherwise they are ignored. See [Topup Parameters](README.md#topup-parameters) in the README.
- **Banners are cross-cutting**: clarified that `flowParams.banners` may be passed regardless of the active flow. The `bannerType` value identifies where the banner is rendered; it is not tied to a specific flow.
- **Topup parameter namespacing**: `flowParams.topup.{amount, currency, suggestedAmounts}` is the new recommended nested shape. `flowParams.topup` may be sent regardless of which `flow` is initialized.

### Fixed

- Fixed the topup amount being lost when navigating back from the amount screen and re-selecting a payment method.

### Removed

- `AstroConfiguration.showCloseButton`, `AstroConfiguration.autoSize`, `AstroConfiguration.Builder.setShowCloseButton`, and `AstroConfiguration.Builder.setAutoSize` (deprecated since v1.0.11). Use `showHeader` / `Builder.setShowHeader` to control header visibility.

### Deprecated

- Flat topup keys `amount`, `currency`, and `suggestedAmounts` placed directly under `flowParams` — use the nested `flowParams.topup` shape instead. The flat keys are still accepted silently for backward compatibility and will be removed in a future major version.

---

## [1.0.13]

### Added

- **Pre-load terminal callback**: new `AstroConnect.preload(configuration, onPreloadEnded)` overload that reports `Loaded`, `Deferred`, or `Failed(error)`.
- `AstroConnect.preload` now has a load timeout — the callback fires with `Failed` instead of hanging if the page never reaches a terminal state.
- **Home Banners documentation**: documented how to render `home-page` and `home-header` promotional banners via `flowParams.banners`. See [Home Banners](README.md#home-banners) in the README.
- **Topup flow parameters documentation**: documented `amount`, `currency`, and `suggestedAmounts` for the topup flow. See [Topup Flow Parameters](README.md#topup-flow-parameters) in the README.

### Fixed

- Fixed an unexpected biometric prompt that could appear before the SDK was visible when calling `AstroConnect.preload`. The prompt is now deferred until `AstroConnectView` is displayed.

### Deprecated

- `AstroConnect.preload(configuration, onSuccess, onError)` — use the new `onPreloadEnded` overload. The old callbacks still work. See the [Migration Guide](migrations/v1.0.12-to-v1.0.13.md).

---

## [1.0.12]

> **Breaking change:** The standalone `AstroConnect(...)` composable has been removed. Use `AstroConnectView(...)` instead. See the [Migration Guide](migrations/v1.0.11-to-v1.0.12.md) for details.

### Added

- **Pre-warming**: new `AstroConnect.preWarm` to initialize the SDK in the background early in the app lifecycle, reducing first-open latency. Optional `appIssuer` parameter prepares the co-branded header logo in advance.
- **Pre-loading**: new `AstroConnect.preload` to load the SDK with a specific configuration before presenting it, so `AstroConnectView` opens with no loading screen.
- **Clear**: new `AstroConnect.clear` to reset SDK data (cookies, local storage, caches). Also discards any active pre-load.

### Removed

- Removed the `AstroConnect(...)` composable. Use `AstroConnectView(...)` to display the SDK. The `AstroConnect` symbol remains as the entry point for the static API (`preWarm`, `preload`, `clear`).

---

## [1.0.11]

### Added

- Added co-branded issuer logo in the SDK header. Controlled via `showHeaderLogo` in `AstroConfiguration`.

---

## [1.0.10]

### Added

- Added `biometricGracePeriod` parameter to control how long biometric re-prompting is suppressed after a successful authentication.
- Added biometric hardware availability check before registration.

### Fixed

- Improved connection stability on certain network configurations.

---

## [1.0.9]

### Fixed

- Resolved an issue that caused the SDK to fail on first load in certain configurations.

---

## [1.0.8]

### Fixed

- Fixed a dependency conflict that could cause build failures in some project configurations.
- General stability improvements.

---

## [1.0.7]

### Added

- Added phone sign-in support.
- Added biometric authentication (2FA). Requires `USE_BIOMETRIC` and `USE_FINGERPRINT` permissions in `AndroidManifest.xml`.

---

## [1.0.6]

### Changed

- Redesigned KYC and address onboarding screens.

---

## [1.0.5]

### Added

- Added external URL support (opens in device browser).
- Added analytics event emission during user flows, accessible via `AstroResult.Event`.

---

## [1.0.4]

### Added

- Access token is now included in the URL builder.

---

## [1.0.3]

### Added

- Added close button to the SDK header.

---

## [1.0.2]

### Added

- Added support for `AstroResult.Event` callbacks during user flows.
- Initial Android SDK release.
