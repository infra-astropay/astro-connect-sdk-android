# Changelog

All notable changes to the AstroConnectSDK for Android will be documented in this file.

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
