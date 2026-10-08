# Flow Parameters - Android

This document describes the optional values you can send in `flowParams` to preset or prefill parts of a flow. `flowParams` is set when you create the [`AstroConfiguration`](README.md#create-configuration).

| `flowParams` key | Purpose |
|------------------|---------|
| `topup` | Preset the amount, the currency, suggested amounts and the minimum amount on the topup amount screen. See [Topup Parameters](#topup-parameters) |
| `onboarding` | Fill in onboarding form fields with values you already hold for the user. See [Onboarding Prefill](#onboarding-prefill) |
| `login` | Fill in the phone number on the login screen. See [Login Phone Prefill](#login-phone-prefill) |

Banners are also sent through `flowParams` and have their own guide: [Banners](BANNERS.md).

## Topup Parameters

Topup parameters are cross-cutting: whenever the user lands on the topup amount screen — regardless of the flow that was initialized — you can preset the amount, the currency, and a list of suggested amounts that are rendered as pills below the amount input. You can also set the minimum amount the user is allowed to enter.

Pass these values under a nested `topup` map inside `flowParams`.

`currency` is required for `amount`, `suggestedAmounts`, and `minAmount` to take effect: if it is omitted, or does not match the currency shown on the screen, none of them is applied.

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
                "minAmount" to 20,
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

### Minimum amount

`minAmount` sets the lowest amount the user may enter on the topup amount screen. Like `suggestedAmounts`, it takes effect only when `currency` matches the currency shown on the screen; sent without a matching `currency`, it is ignored.

For partners that operate multiple currencies, `minAmountByCurrency` sets the minimum per currency instead of (or alongside) the flat `minAmount`. Keys are ISO 4217 codes (case-insensitive — they are normalized to uppercase internally):

```kotlin
// Per-currency minimum map — overrides `minAmount` when present.
// Keys are ISO 4217 codes (case-insensitive).
val flowParams = mapOf(
    "topup" to mapOf(
        "currency" to "USD",
        "minAmount" to 20,
        "minAmountByCurrency" to mapOf(
            "USD" to 20,
            "EUR" to 20,
            "BRL" to 100
        )
    )
)
```

> **Precedence:** When both `minAmount` and `minAmountByCurrency` are provided, the per-currency map wins. If the user is on a currency that is not a key in the map, no partner minimum is applied — the flat `minAmount` is NOT consulted as a fallback.

A partner minimum can only raise the floor the app already applies; it never lowers it. When the user enters less than the minimum, the amount screen shows its minimum-amount error and the continue button stays disabled. Suggested amounts below the minimum are not rendered as pills. Duplicate values are removed and at most four pills are shown — the first four valid amounts in the order you sent them. AstroPay's own limits still apply on top of the value you send: a partner minimum does not override a rejection returned when the amount is confirmed.

> **Deprecated:** The flat keys `amount`, `currency`, and `suggestedAmounts` placed directly under `flowParams` are still accepted for backward compatibility, but the nested `flowParams.topup` shape is the recommended form. The flat keys will be removed in a future major version.

### Topup Parameters

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `amount` | `Double` | No | Pre-fills the amount input. Requires `currency` and only applied when it matches the screen currency |
| `currency` | `String` | No | Target currency (ISO 4217 code, e.g., `"USD"`). Required for `amount`, `suggestedAmounts`, and `minAmount` to take effect |
| `suggestedAmounts` | `List<Double>` | No | List of positive amounts rendered as clickable pills below the input. Tapping a pill sets the input to that value. Requires `currency` and only rendered when it matches the screen currency. Ignored entirely when `suggestedAmountsByCurrency` is provided |
| `suggestedAmountsByCurrency` | `Map<String, List<Double>>` | No | Per-currency preset map. Keys are ISO 4217 codes (case-insensitive). When present, takes precedence over `suggestedAmounts`; if the screen currency is not a key in the map, no preset pills are shown |
| `minAmount` | `Double` | No | Lowest amount the user may enter. Requires `currency` and only applied when it matches the screen currency. Can only raise the minimum the app already applies, never lower it. Ignored entirely when `minAmountByCurrency` is provided |
| `minAmountByCurrency` | `Map<String, Double>` | No | Per-currency minimum map. Keys are ISO 4217 codes (case-insensitive). When present, takes precedence over `minAmount`; if the screen currency is not a key in the map, no partner minimum is applied |

## Onboarding Prefill

Onboarding prefill lets you fill in some of the onboarding form fields with values you already
hold for the user — for example the email address and personal data collected when they signed up
in your app. Prefilled values are editable: the user still reviews and confirms the form before
submitting it.

Pass these values under a nested `onboarding` map inside `flowParams`. **No SDK update is
required** — the values travel inside the existing `flowParams`, so this works on any installed SDK
version.

```kotlin
val configuration = AstroConfiguration.builder()
    .setEnvironment("sandbox")
    .setAppIssuer("your-app-issuer")
    .setClientId("your-client-id")
    .setPartnerUserId("your-partner-user-id")
    .setAccessToken("your-access-token")
    .setFlowParams(
        mapOf(
            "onboarding" to mapOf(
                "stepEmail" to mapOf(
                    "email" to "user@example.com",
                ),
                "stepPersonalData" to mapOf(
                    "firstName" to "Ale",
                    "lastName" to "Caballero",
                    "dob" to "1990-05-14",
                    "documentNumber" to "210.196.573-91",
                    "motherName" to "Maria Caballero",
                ),
                "stepAddress" to mapOf(
                    "postalCode" to "01310-100",
                ),
            ),
        ),
    )
    .build()
```

Only `stepEmail`, `stepPersonalData` and `stepAddress` are recognized. Other step keys are
accepted and ignored.

Field keys are matched leniently — case and separators are ignored, so `firstName`, `first_name`
and `First Name` all reach the same field. Which fields a step contains is decided by AstroPay and
can vary by country and partner, so a key that matches no field in the step is ignored. If two keys
in the same step resolve to the same field (for example `firstName` and `first_name`), neither is
applied.

### Onboarding Parameters

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `stepEmail` | `Map<String, Any>` | No | Field values for the email step |
| `stepPersonalData` | `Map<String, Any>` | No | Field values for the personal data step |
| `stepAddress` | `Map<String, Any>` | No | Postal code for the address step — `postalCode` is the only accepted field |

### Step Fields

| Step | Field | Type | Description |
|------|-------|------|-------------|
| `stepEmail` | `email` | `String` | Email address to prefill |
| `stepPersonalData` | `firstName` | `String` | Given name to prefill |
| `stepPersonalData` | `lastName` | `String` | Family name to prefill |
| `stepPersonalData` | `dob` | `String` | Date of birth as `YYYY-MM-DD` (e.g., `"1990-05-14"`). Do not send a timestamp |
| `stepPersonalData` | `documentNumber` | `String` | Identity document number to prefill. Both the formatted and the digits-only form are accepted (e.g., `"210.196.573-91"` or `"21019657391"`) |
| `stepPersonalData` | `motherName` | `String` | Mother's name to prefill. Brazil only |
| `stepAddress` | `postalCode` | `String` | Postal code / CEP. Both the formatted and the digits-only form are accepted (e.g., `"01310-100"` or `"01310100"`) |

> **Existing values win:** prefill only fills fields that are still empty. Any value AstroPay
> already holds for the user is kept and never overwritten.

> **Not prefillable:** checkboxes and multi-select fields cannot be prefilled — consent and legal
> declarations must be made by the user. The PIN field can never be prefilled.

> **Document number formatting:** `documentNumber` is accepted with or without separators — dots,
> hyphens and spaces are ignored when the value is checked. Send whichever form you hold; the user
> can edit it before submitting.

> **Address prefill covers the postal code only:** `postalCode` prefills the postal-code lookup
> screen that opens the address step. The rest of the address is completed from that lookup, so no
> other address field is accepted. The user still confirms the postal code — nothing is submitted
> automatically.

> **Unknown or invalid values are ignored silently:** nothing is shown to the user as an error and
> nothing is reported back to your app. This covers unrecognized step keys, field keys that match no
> field in the step, and a `dob` that is not in `YYYY-MM-DD` format or is not a real calendar date
> (for example `1990-02-31`).

> **Privacy:** send only data the user has consented to share with AstroPay. The values are used to
> prefill an editable form that the user still confirms.

## Login Phone Prefill

Login phone prefill fills in the phone number on the login screen, so the user does not have to type
a number you already hold for them. The value is editable: the user still reviews and confirms it
before continuing.

Pass these values under a nested `login` map inside `flowParams`. **No SDK update is required** —
the values travel inside the existing `flowParams`, so this works on any installed SDK version.

```kotlin
val configuration = AstroConfiguration.builder()
    .setEnvironment("sandbox")
    .setAppIssuer("your-app-issuer")
    .setClientId("your-client-id")
    .setPartnerUserId("your-partner-user-id")
    .setFlowParams(
        mapOf(
            "login" to mapOf(
                "phoneCode" to "55",
                "phoneNumber" to "11987654321",
            ),
        ),
    )
    .build()
```

### Login Parameters

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `phoneCode` | `String` | No | Phone country code, digits only (e.g., `"55"`, `"54"`) |
| `phoneNumber` | `String` | No | Phone number without the country code, digits only (e.g., `"11987654321"`) |

> **Digits only:** leading and trailing spaces are removed first. A value that still contains `+`,
> spaces between digits, hyphens or any other non-digit character is ignored and the field is left
> empty.

> **Relation to `phoneCode` / `phoneNumber`:** the configuration parameters `phoneCode` and
> `phoneNumber` keep working. When a value is sent in both places, the one in `flowParams.login`
> is used. Each field is resolved on its own, so you can send one field in `flowParams.login` and
> keep the other in the configuration. An empty value in `flowParams.login` counts as not sent; a
> non-empty value that is not digits only still takes precedence, so the field is left empty instead
> of using the configuration value.
