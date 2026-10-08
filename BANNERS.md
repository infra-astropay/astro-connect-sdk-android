# Banners - Android

This document describes the banners you can show inside the SDK: where they appear, what they contain, and what happens when the user acts on or dismisses them. Banners are sent in `flowParams` when you create the [`AstroConfiguration`](README.md#create-configuration).

Banners are cross-cutting: you can pass them via `flowParams.banners` regardless of the active flow. The `bannerType` value determines where each banner is rendered, not the flow that was initialized. Five placements are supported:

- `home-page` — full-screen banner shown once per session before the home loads (e.g. onboarding).
- `home-header` — compact banner rendered at the top of the home.
- `topup-select-method` — compact banner rendered on the payment method selection screen of the add-money flow.
- `topup-select-currency` — compact banner rendered on the currency selection screen of the add-money flow. That screen is skipped for users who hold a single wallet, so a banner here is only seen by users with more than one currency.
- `ob-kyc-start` — compact banner rendered on the identity validation step of the onboarding flow, above the screen title.

`home-header`, `topup-select-method`, `topup-select-currency`, and `ob-kyc-start` all use the same horizontal strip layout: you can place several banners on the same surface, they scroll horizontally, and each one can be dismissed independently. `home-page` is the only full-screen placement, and it shows one banner: if you send more than one `home-page` banner, only the first one in the list is used.

All banner fields below apply to every placement — to use a new surface, send the same banner payload with a different `bannerType`.

Each banner is a map inside the `banners` list.

By default a banner's action navigates to a destination inside the SDK. Set `bannerLinkTarget` to `"external"` to send the user outside instead — to a website or to one of your own app's screens. See [Banner Link Target](#banner-link-target).

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
                mapOf(
                    "bannerType" to "home-header",
                    "bannerTitle" to "See our latest promotions",
                    "bannerDescription" to "Open the promotions page on our site.",
                    "bannerActionText" to "View",
                    "bannerDeepLink" to "https://example.com/promotions",
                    "bannerLinkTarget" to "external",
                    "bannerImage" to "banner-home-header-promos-en",
                ),
                mapOf(
                    "bannerType" to "topup-select-method",
                    "bannerTitle" to "Pay with card and save",
                    "bannerDescription" to "No fees on card top-ups this month.",
                    "bannerActionText" to "Learn more",
                    "bannerDeepLink" to "https://example.com/promotions",
                    "bannerLinkTarget" to "external",
                    "bannerDismissible" to true,
                    "bannerImage" to "banner-topup-method-en",
                ),
            ),
        ),
    )
    .build()
```

## Banner Parameters

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `bannerType` | `String` | Yes | Banner placement: `"home-page"`, `"home-header"`, `"topup-select-method"`, `"topup-select-currency"` or `"ob-kyc-start"` |
| `bannerTitle` | `String?` | No | Title text. A banner needs a `bannerTitle` or a `bannerDescription` — unless it uses `bannerAutoRedirect`, which needs neither |
| `bannerDescription` | `String?` | No | Description text. See `bannerTitle` |
| `bannerActionText` | `String?` | No | Primary action button label. If omitted on a strip banner (`home-header`, `topup-select-method`, `topup-select-currency`, `ob-kyc-start`), the entire banner stays clickable and a chevron is shown instead — unless the banner also shows a close button, which takes its place |
| `bannerDismissText` | `String?` | No | Dismiss button label (only used by `home-page`). Ignored when `bannerDismissible` is `false` |
| `bannerDeepLink` | `String` | Yes | Destination opened on action. With the default `bannerLinkTarget`, one of `"topup"`, `"activities"`, `"cards"`, `"withdrawal"`, `"check_cci"`, `"account_closure"`, or a `deeplink://` destination — see [Internal destinations](#internal-destinations). With `bannerLinkTarget: "external"`, a full link — see [Banner Link Target](#banner-link-target) |
| `bannerLinkTarget` | `String?` | No | Where `bannerDeepLink` opens: `"internal"` (default) or `"external"`. See [Banner Link Target](#banner-link-target) |
| `bannerDismissible` | `Boolean?` | No | Meaning depends on the banner layout. On a strip banner (`home-header`, `topup-select-method`, `topup-select-currency`, `ob-kyc-start`) it shows a close button, independent of the banner's action — a banner can be actionable and dismissible at the same time. On a full-screen `home-page` banner, `false` makes the banner blocking. See [Dismissing a strip banner](#dismissing-a-strip-banner) and [Blocking a full-screen banner](#blocking-a-full-screen-banner) |
| `bannerAfterAction` | `String?` | No | What happens to the banner, and to the SDK, once the user takes the banner's action: `"closeBanner"`, `"closeSDK"` or `"stick"`. When omitted, a `home-page` banner behaves as `"closeBanner"` and a strip banner as `"stick"`. See [After the banner action](#after-the-banner-action) |
| `bannerAutoRedirect` | `Boolean?` | No | Only used by `home-page`. When `true`, the banner is never shown: the user is taken straight to its internal `bannerDeepLink`. See [Redirecting instead of showing a full-screen banner](#redirecting-instead-of-showing-a-full-screen-banner) |
| `bannerImage` | `String?` | No | Image asset name to render on the banner |
| `bannerImageSize` | `String?` | No | Image size for `home-page` banners. Accepts a CSS length in `px`, `vh`, or `vw` (e.g. `"200px"`, `"50vh"`, `"40vw"`). Defaults to `30vh` when omitted or invalid |

## Banner Link Target

`bannerLinkTarget` controls whether the banner action stays inside the SDK or leaves it.

| Value | Behavior | `bannerDeepLink` format |
|-------|----------|-------------------------|
| `"internal"` (default) | Navigates to a destination inside the SDK | One of `"topup"`, `"activities"`, `"cards"`, `"withdrawal"`, `"check_cci"`, `"account_closure"`, or a `deeplink://` destination — see [Internal destinations](#internal-destinations) |
| `"external"` | Leaves the SDK and hands the link to the system | A full link, either an `https://` URL or a custom scheme |

### Internal destinations

When `bannerLinkTarget` is `"internal"` or omitted, `bannerDeepLink` accepts:

- `"topup"`, `"activities"`, `"cards"`, `"withdrawal"` — unchanged.
- `"check_cci"` — opens the screen that tracks the creation of the user's CCI account. See [Waiting for the user's CCI account](#waiting-for-the-users-cci-account).
- `"account_closure"` — opens the account closure flow.
- A `deeplink://` destination from the table below.

**Account closure.** `"account_closure"` opens the account closure flow, where the user goes through the closure steps. If the user completes the flow and closes the account, the session ends and the SDK closes as it does when the user signs out (`CLOSED_BY_USER_SIGNED_OUT`). If the user leaves the flow without closing the account, they return to the screen that showed the banner.

```kotlin
mapOf(
    "bannerType" to "home-header",
    "bannerTitle" to "Close your account",
    "bannerDescription" to "Review the steps to close your account.",
    "bannerActionText" to "Continue",
    "bannerDeepLink" to "account_closure",
)
```

**`deeplink://` destinations.** A value in the format `deeplink://<destination>` opens one of the destinations below. When the user exits a destination opened this way, they return to the screen that showed the banner.

| `bannerDeepLink` | Opens |
|------------------|-------|
| `deeplink://home_main` | Home |
| `deeplink://add_funds` | Add money (top up) |
| `deeplink://send` | Send money |
| `deeplink://send_and_receive` | Send and receive |
| `deeplink://exchange_money` | Currency exchange |
| `deeplink://activities` | Activity / movements |
| `deeplink://pix` | PIX (Brazil accounts only) |
| `deeplink://debit_card_detail` | Cards |
| `deeplink://my_account` | My account |
| `deeplink://security` | Security settings |
| `deeplink://help_center_dynamic_workflow?workflowType=ACCOUNT_CLOSURE` | Account closure (same as `"account_closure"`) |

```kotlin
mapOf(
    "bannerType" to "home-header",
    "bannerTitle" to "Exchange at a great rate",
    "bannerDescription" to "Convert between your currencies in seconds.",
    "bannerActionText" to "Exchange",
    "bannerDeepLink" to "deeplink://exchange_money",
)
```

Not every destination is available for every account, country, or partner. A banner pointing to an unavailable destination does nothing when tapped. Leading and trailing whitespace in `bannerDeepLink` is ignored, and an unrecognized value does nothing when tapped.

When `bannerLinkTarget` is `"external"`, the format of `bannerDeepLink` decides what opens — you do not need to declare it separately:

- `"https://example.com/promotions"` opens in the device browser.
- `"yourapp://promotions"` opens the matching screen in your own app.

Only pass links the device can resolve. A custom scheme with no app installed to handle it cannot be opened. Any value of `bannerLinkTarget` other than `"external"` is treated as `"internal"`.

Omitting `bannerLinkTarget` keeps the previous behavior, so existing banner payloads need no changes.

<a id="dismissing-a-header-banner"></a>

## Dismissing a strip banner

`bannerDismissible` adds a close button to a strip banner (`home-header`, `topup-select-method`, `topup-select-currency` or `ob-kyc-start`), letting the user hide it for the rest of the session. This section covers strip banners only — for the full-screen `home-page` layout, see [Blocking a full-screen banner](#blocking-a-full-screen-banner). It is independent of the banner's action, so all four combinations are valid:

| `bannerDeepLink` | `bannerDismissible` | Result |
|---|---|---|
| A destination | `true` | Banner is tappable **and** shows a close button |
| A destination | omitted | Banner is tappable, no close button |
| `"none"` | omitted | Close button only — the banner is not tappable |
| `"none"` | `false` | Neither: the banner just displays its content |

```kotlin
mapOf(
    "bannerType" to "home-header",
    "bannerTitle" to "See our latest promotions",
    "bannerActionText" to "View",
    "bannerDeepLink" to "https://example.com/promotions",
    "bannerLinkTarget" to "external",
    "bannerDismissible" to true,
)
```

When omitted, `bannerDismissible` keeps the previous behavior: a banner with `bannerDeepLink` set to `"none"` is dismissible, and any other banner is not. Existing payloads need no changes.

A dismissal lasts for the current session and is remembered per banner, identified by its title, description, and image. Banners sharing the same surface are dismissed independently — closing one leaves the others in place.

<a id="after-the-banner-action"></a>

## After the banner action

`bannerAfterAction` decides what happens to the banner — and to the SDK — once the user has taken the banner's action: the action button, or a tap on the banner itself on a strip banner.

| Value | Result |
|---|---|
| `"closeBanner"` | The banner is marked as seen and does not come back for the rest of the session |
| `"closeSDK"` | The SDK closes behind the action. The banner is **not** marked as seen, so it is shown again the next time the SDK opens |
| `"stick"` | Nothing happens to the banner — it is still there when the user comes back to the screen |

When `bannerAfterAction` is omitted, the behavior depends on the banner layout:

| Layout | Behavior when omitted |
|---|---|
| Full-screen `home-page` | As `"closeBanner"` — what a takeover has always done |
| Strip banners (`home-header`, `topup-select-method`, `topup-select-currency`, `ob-kyc-start`) | As `"stick"` — a strip banner has always stayed in its list after being tapped |

Omitting `bannerAfterAction` changes nothing for existing banner payloads on either layout.

### Closing the SDK from a banner

`"closeSDK"` is how you send the user into your own app and leave them there. It combines with `bannerLinkTarget` as follows:

- With `bannerLinkTarget: "external"`, the link is handed to the operating system first and the SDK closes behind it. This is the main use case.
- An **internal** destination is dropped under `"closeSDK"`: a screen inside an SDK that is closing would be torn down as it opens. So `bannerDeepLink: "none"` together with `"closeSDK"` is a plain "leave" button that just closes the SDK.

When the SDK closes this way, your app receives the normal close callback with code `CLOSED_BY_USER_BANNER_ACTION` and a message naming the banner type — see [Close payload](README.md#close-payload).

```kotlin
mapOf(
    "bannerType" to "home-header",
    "bannerTitle" to "Continue in our app",
    "bannerDescription" to "Track your order from your account.",
    "bannerActionText" to "Open my orders",
    "bannerDeepLink" to "yourapp://orders",
    "bannerLinkTarget" to "external",
    "bannerAfterAction" to "closeSDK",
    "bannerImage" to "banner-home-header-orders-en",
)
```

## Blocking a full-screen banner

While a full-screen `home-page` banner is on screen, the SDK also hides its own navigation menu, so the banner really is the whole screen.

On a `home-page` banner (full-screen takeover), `bannerDismissible` decides whether the user can get past the banner:

| `bannerDismissible` | Result |
|---|---|
| omitted or `true` | Default behavior. A dismiss button is shown when `bannerDismissText` is set, and dismissing or using the action hides the banner for the rest of the session |
| `false` | Blocking. No dismiss button is shown — even if `bannerDismissText` is set — the banner is shown again every time the user returns to the screen, and navigation back through the SDK's own screens is disabled while it is on screen |

Use `false` for hard blocks, such as requiring the user to update before continuing. The banner's action (`bannerActionText` + `bannerDeepLink`) keeps working on a blocking banner — it is the way forward you choose to offer. Using the action does not mark the banner as seen, so the banner appears again the next time the user reaches the screen.

Only an explicit `false` makes a `home-page` banner blocking. The `bannerDeepLink` value `"none"` described above applies to strip banners and has no effect here, so existing `home-page` payloads need no changes.

**With `bannerAfterAction`.** On a blocking banner, `"closeBanner"` is ignored and treated as `"stick"`: letting the action mark the banner as seen would reopen exactly the way out that the block removes. `"closeSDK"` is honored, and it is the intended pairing for a forced-update style block — the user leaves for your update link and the SDK closes behind them, with the banner shown again the next time the SDK opens. See [After the banner action](#after-the-banner-action).

```kotlin
mapOf(
    "bannerType" to "home-page",
    "bannerTitle" to "Update required",
    "bannerDescription" to "Install the latest version to keep using your wallet.",
    "bannerActionText" to "Update now",
    "bannerDeepLink" to "https://example.com/update",
    "bannerLinkTarget" to "external",
    "bannerDismissible" to false,
    "bannerAfterAction" to "closeSDK",
    "bannerImage" to "banner-home-page-update-en",
)
```

> **Your app must complete the block.** The SDK cannot intercept the device's back button or system back gesture, so a blocking banner only blocks navigation between the SDK's own screens. If you need a true hard block, also prevent the user from closing or backing out of the screen that hosts the SDK while a blocking banner is in play.

## Redirecting instead of showing a full-screen banner

With `bannerAutoRedirect: true`, a `home-page` banner is never shown. When the user reaches the home screen — at the moment the banner would have appeared — the SDK takes them straight to the banner's `bannerDeepLink` instead. Every display field (`bannerTitle`, `bannerDescription`, `bannerImage`, button labels) is ignored, and none of them is required.

`bannerDismissible` still decides whether this happens once or on every visit:

| Configuration | Result |
|---|---|
| Default (`bannerDismissible` omitted or `true`) | The user is redirected once; the home screen shows normally for the rest of the session |
| `bannerDismissible: false` | The user is redirected every time they return to the home screen, until the destination itself lifts the block (see `check_cci` below) |

An explicit `bannerAfterAction` of `"stick"` or `"closeSDK"` also makes the redirect repeat on every visit, even without `bannerDismissible: false`: only the default (`"closeBanner"`) marks the banner as seen.

`bannerAutoRedirect` has no effect in these cases, and the banner is displayed as usual (or skipped, if it has no title or description):

- Strip banners (`home-header`, `topup-select-method`, `topup-select-currency`, `ob-kyc-start`).
- `bannerLinkTarget: "external"` — the SDK does not open a browser or another app without the user asking for it.
- A `bannerDeepLink` other than `"topup"`, `"activities"`, `"cards"`, `"withdrawal"` or `"check_cci"`. `"account_closure"` and `deeplink://` destinations cannot be auto-redirected to.

### Waiting for the user's CCI account

`check_cci` opens a screen that tracks the creation of the user's CCI account and keeps it up to date until the account is ready:

| Account status | What the user sees |
|---|---|
| Being created | A progress screen with a loader that refreshes on its own. After a minute it says the creation is taking longer than expected and offers an option to come back later. After about two minutes, a dialog asks the user whether to keep waiting or come back later; refreshing pauses until they choose |
| Created | A confirmation screen. Continuing takes the user to the home screen and lifts a blocking `check_cci` banner for the rest of the session |
| Rejected | An error screen, pointing to support for more information, with an "OK" button that closes the SDK |
| Status could not be retrieved | An error screen with an option to check again and one to come back later |

When AstroPay requires the user to set or confirm their email before going on, the screen shows an email-required step instead of the progress, confirmation or rejection screen, whatever the account status is. "Continue" takes the user to the email verification screen inside the SDK and brings them back here afterwards, where the status is checked again; the step also offers an option to come back later.

Only a created account with no email step left lets the user through; every other outcome keeps them on this screen.

Every other option to leave reads "Come back later" and closes the SDK. It reports code `CLOSED_BY_USER_NAVIGATED_BACK` from the progress screen, the email-required step or the dialog, and `CLOSED_BY_USER_DISMISSED_ERROR` from the error screens — see [Close payload](README.md#close-payload). Each state and button also emits an event on the `cci_account_check` screen — see [Events](README.md#events).

To keep a user out of the wallet until their CCI account exists, pair it with a blocking, auto-redirecting banner:

```kotlin
mapOf(
    "bannerType" to "home-page",
    "bannerDeepLink" to "check_cci",
    "bannerAutoRedirect" to true,
    "bannerDismissible" to false,
)
```
