# Style Tokens Reference - Android

This document describes the full catalog of design tokens accepted by [`AstroStyle`](README.md#style-customization) and how brand-level overrides cascade to individual tokens.

## Overview

`AstroStyle` exposes the following slots:

| Slot              | Type                   | Purpose                                                                                                                            |
|-------------------|------------------------|------------------------------------------------------------------------------------------------------------------------------------|
| `backgroundColor` | `@ColorInt Int?`       | Main background color. Also drives the initial loading screen and cascades to `surface.base` when unset.                           |
| `primaryColor`    | `@ColorInt Int?`       | Primary brand color. Cascades to several highlight/button tokens when those are unset (see [Cascade rules](#cascade-rules)).       |
| `surface`         | `AstroSurfaceColors?`  | Background fills for containers, cards, banners and overlays.                                                                      |
| `text`            | `AstroTextColors?`     | Foreground colors for typography.                                                                                                  |
| `border`          | `AstroBorderColors?`   | Stroke colors for outlines, dividers, and separators.                                                                              |
| `typography`      | `AstroTypography?`     | Global typography settings. Exposes a single field, `fontFamily: String?`, used as the default font family across the SDK.   |
| `buttons`         | `AstroButtonStyle?`    | Wrapper around `AstroButtonColors` (12 variants × 11 props) and an optional `AstroButtonTypography` slot.                          |
| `buttonsIcon`     | `AstroButtonIconStyle?` | Wrapper around `AstroButtonIconColors` (same 12 variants, icon-specific props) and an optional `AstroButtonIconTypography` slot.   |
| `buttonsPill`     | `AstroButtonPillStyle?` | Wrapper around `AstroButtonPillColors` (14 status pills × 5 props) and an optional `AstroButtonPillTypography` slot.               |
| `inputs`          | `AstroInputStyle?`     | Wrapper around `AstroInputColors` (text inputs, dropdown, phone-country dropdown) and an optional `AstroInputTypography` slot.     |
| `header`          | `AstroHeaderStyle?`    | Typed header layout and colors (see [README](README.md#astroheaderstyle)).                                                         |

All color values are `@ColorInt Int?` — see [Color values](#color-values) below for the accepted construction forms and alpha support. Per-component typography values are plain numbers (see [`AstroFontStyle`](#astrofontstyle) for accepted formats).

## Cascade rules

The brand-level fields (`backgroundColor`, `primaryColor`) propagate to related tokens **only when those tokens are not set explicitly**. Explicit values always win. Typography does not cascade — each per-component slot is applied only where it is set.

| Brand field       | Cascades to (when token unset)                                                                  |
|-------------------|-------------------------------------------------------------------------------------------------|
| `backgroundColor` | `surface.base`                                                                                  |
| `primaryColor`    | `surface.highlight`, `text.highlight`, `border.highlight`                                       |

Example: setting `backgroundColor = Color(0xFF041311).toArgb()` and leaving `surface.base` unset is equivalent to setting `surface.base = Color(0xFF041311).toArgb()`. Setting both explicitly will use the value passed to `surface`.

```kotlin
// Color.White wins for surface.base; primaryColor still cascades to the
// other highlight/button tokens because they are not set.
val style = AstroStyle(
    backgroundColor = Color(0xFF041311).toArgb(),
    primaryColor = Color(0xFF00DBBF).toArgb(),
    surface = AstroSurfaceColors(base = Color.White.toArgb()),
)
```

## Color values

Every color field on the typed `AstroStyle` surface is a `@ColorInt Int?` — the same color representation used across the Android platform (Compose, View XML, `ContextCompat`, etc.). Any of the following construction forms work:

```kotlin
// 1. Compose Color converted to an @ColorInt Int
val red    = Color.Red.toArgb()
val brand  = Color(0xFF0033CC).toArgb()

// 2. A resource looked up through Context
val brand2 = ContextCompat.getColor(context, R.color.brand_primary)

// 3. A literal Int — useful when you don't have a Context handy
val red2   = 0xFFFF0000.toInt()
```

Alpha is honored. `0x80FF0000` (50%-opacity red) round-trips on the wire as `#FF000080` — fully opaque colors keep emitting 6-digit `#RRGGBB`, partially transparent colors emit 8-digit `#RRGGBBAA`.

> The typed `AstroStyle` API exposes color fields as `@ColorInt Int`. The escape-hatch `styleOverrides` map still uses hex strings (`"#RRGGBB"` or `"#RRGGBBAA"`) because it is a free-form passthrough — see [Free-form overrides via `styleOverrides`](#free-form-overrides-via-styleoverrides).

## Token reference

### `AstroSurfaceColors`

Background fills for containers, cards, banners and overlays. Most tokens come in three variants: the base color, a `Hover` variant for pointer hover/press, and an `Active` variant for the pressed state. Some semantic tokens also expose a lighter `Light` variant.

Every field below is an optional `@ColorInt Int?` — see [Color values](#color-values).

| Field                  | Description                          |
|------------------------|--------------------------------------|
| `base`                 | App background                       |
| `baseHover`            | Base / hover                         |
| `baseActive`           | Base / pressed                       |
| `secondary`            | Secondary container                  |
| `secondaryHover`       | Secondary / hover                    |
| `secondaryActive`      | Secondary / pressed                  |
| `terciary`             | Tertiary container                   |
| `terciaryHover`        | Tertiary / hover                     |
| `terciaryActive`       | Tertiary / pressed                   |
| `muted`                | Muted/neutral container              |
| `mutedHover`           | Muted / hover                        |
| `mutedActive`          | Muted / pressed                      |
| `contrast`             | High-contrast container              |
| `contrastHover`        | Contrast / hover                     |
| `contrastActive`       | Contrast / pressed                   |
| `invert`               | Inverted-theme container             |
| `invertHover`          | Invert / hover                       |
| `invertActive`         | Invert / pressed                     |
| `white`                | Fixed-white container                |
| `whiteHover`           | White / hover                        |
| `whiteActive`          | White / pressed                      |
| `highlight`            | Brand highlight (filled accent)      |
| `highlightHover`       | Highlight / hover                    |
| `highlightActive`      | Highlight / pressed                  |
| `highlightMuted`       | Subdued brand highlight              |
| `highlightMutedHover`  | Highlight muted / hover              |
| `highlightMutedActive` | Highlight muted / pressed            |
| `error`                | Error/destructive container          |
| `errorHover`           | Error / hover                        |
| `errorActive`          | Error / pressed                      |
| `errorMuted`           | Subdued error container              |
| `errorMutedHover`      | Error muted / hover                  |
| `errorMutedActive`     | Error muted / pressed                |
| `errorLight`           | Lightest error tone (info banner)    |
| `warning`              | Warning container                    |
| `warningHover`         | Warning / hover                      |
| `warningActive`        | Warning / pressed                    |
| `warningMuted`         | Subdued warning container            |
| `warningMutedHover`    | Warning muted / hover                |
| `warningMutedActive`   | Warning muted / pressed              |
| `warningLight`         | Lightest warning tone                |
| `success`              | Success container                    |
| `successHover`         | Success / hover                      |
| `successActive`        | Success / pressed                    |
| `successMuted`         | Subdued success container            |
| `successMutedHover`    | Success muted / hover                |
| `successMutedActive`   | Success muted / pressed              |
| `successLight`         | Lightest success tone                |
| `accent`               | Secondary accent container           |
| `accentHover`          | Accent / hover                       |
| `accentActive`         | Accent / pressed                     |
| `accentMuted`          | Subdued accent container             |
| `accentMutedHover`     | Accent muted / hover                 |
| `accentMutedActive`    | Accent muted / pressed               |
| `accentLight`          | Lightest accent tone                 |
| `lime`                 | Lime decorative tone                 |
| `limeMuted`            | Subdued lime tone                    |
| `overlay`              | Modal/scrim overlay                  |
| `black`                | Fixed-black container                |

### `AstroTextColors`

Foreground colors for typography. Every field below is an optional `@ColorInt Int?` — see [Color values](#color-values).

| Field            | Description                                |
|------------------|--------------------------------------------|
| `black`          | Fixed black                                |
| `white`          | Fixed white                                |
| `base`           | Default body text                          |
| `muted`          | De-emphasized body text                    |
| `mutedSecondary` | Secondary de-emphasized text               |
| `faint`          | Faintest text (placeholders, hints)        |
| `invert`         | Text on inverted-theme surfaces            |
| `highlight`      | Brand-colored text (links, accents)        |
| `error`          | Error / destructive text                   |
| `accent`         | Secondary accent text                      |
| `warning`        | Warning text                               |
| `success`        | Success text                               |

### `AstroBorderColors`

Stroke colors for outlines, dividers, and separators. Every field below is an optional `@ColorInt Int?` — see [Color values](#color-values).

| Field       | Description                       |
|-------------|-----------------------------------|
| `base`      | Default container border          |
| `faint`     | Subtle dividers                   |
| `muted`     | Muted dividers                    |
| `highlight` | Brand-colored border              |
| `error`     | Error border                      |
| `accent`    | Accent border                     |
| `warning`   | Warning border                    |
| `success`   | Success border                    |

### `AstroButtonStyle`

Wrapper for the `buttons` slot. Both fields optional.

| Field        | Type                       | Description                                                                |
|--------------|----------------------------|----------------------------------------------------------------------------|
| `colors`     | `AstroButtonColors?`       | 12 variants × 11 props = 132 tokens (see `colors:` table below).            |
| `typography` | `AstroButtonTypography?`   | Single `label` slot of type [`AstroFontStyle?`](#astrofontstyle) for visible button text. |

#### `colors:` → `AstroButtonColors`

Buttons expose **12 variants × 11 props = 132 tokens**, aligned with the design system. Variants: `primary`, `secondary`, `tertiary`, `gray`, `muted`, `transparent`, `transparentWhite`, `errorMuted`, `error`, `warningMuted`, `warning`, `white`. Each variant has the same set of state-driven props (background, text, loader, focus outline). Every field is an optional `@ColorInt Int?` — see [Color values](#color-values).

| Field | Type | Description |
|-------|------|-------------|
| `primaryBackground` | `@ColorInt Int?` | Primary button — default background |
| `primaryBackgroundHover` | `@ColorInt Int?` | Primary button — hover background |
| `primaryBackgroundFocus` | `@ColorInt Int?` | Primary button — focus background |
| `primaryBackgroundDisabled` | `@ColorInt Int?` | Primary button — disabled background |
| `primaryText` | `@ColorInt Int?` | Primary button — text |
| `primaryTextHover` | `@ColorInt Int?` | Primary button — hover text |
| `primaryTextFocus` | `@ColorInt Int?` | Primary button — focus text |
| `primaryTextDisabled` | `@ColorInt Int?` | Primary button — disabled text |
| `primaryLoaderColor` | `@ColorInt Int?` | Primary button — loader |
| `primaryLoaderColorDisabled` | `@ColorInt Int?` | Primary button — disabled loader |
| `primaryFocusOutlineColor` | `@ColorInt Int?` | Primary button — focus outline |
| `secondaryBackground` | `@ColorInt Int?` | Secondary button — default background |
| `secondaryBackgroundHover` | `@ColorInt Int?` | Secondary button — hover background |
| `secondaryBackgroundFocus` | `@ColorInt Int?` | Secondary button — focus background |
| `secondaryBackgroundDisabled` | `@ColorInt Int?` | Secondary button — disabled background |
| `secondaryText` | `@ColorInt Int?` | Secondary button — text |
| `secondaryTextHover` | `@ColorInt Int?` | Secondary button — hover text |
| `secondaryTextFocus` | `@ColorInt Int?` | Secondary button — focus text |
| `secondaryTextDisabled` | `@ColorInt Int?` | Secondary button — disabled text |
| `secondaryLoaderColor` | `@ColorInt Int?` | Secondary button — loader |
| `secondaryLoaderColorDisabled` | `@ColorInt Int?` | Secondary button — disabled loader |
| `secondaryFocusOutlineColor` | `@ColorInt Int?` | Secondary button — focus outline |
| `tertiaryBackground` | `@ColorInt Int?` | Tertiary button — default background |
| `tertiaryBackgroundHover` | `@ColorInt Int?` | Tertiary button — hover background |
| `tertiaryBackgroundFocus` | `@ColorInt Int?` | Tertiary button — focus background |
| `tertiaryBackgroundDisabled` | `@ColorInt Int?` | Tertiary button — disabled background |
| `tertiaryText` | `@ColorInt Int?` | Tertiary button — text |
| `tertiaryTextHover` | `@ColorInt Int?` | Tertiary button — hover text |
| `tertiaryTextFocus` | `@ColorInt Int?` | Tertiary button — focus text |
| `tertiaryTextDisabled` | `@ColorInt Int?` | Tertiary button — disabled text |
| `tertiaryLoaderColor` | `@ColorInt Int?` | Tertiary button — loader |
| `tertiaryLoaderColorDisabled` | `@ColorInt Int?` | Tertiary button — disabled loader |
| `tertiaryFocusOutlineColor` | `@ColorInt Int?` | Tertiary button — focus outline |
| `grayBackground` | `@ColorInt Int?` | Gray button — default background |
| `grayBackgroundHover` | `@ColorInt Int?` | Gray button — hover background |
| `grayBackgroundFocus` | `@ColorInt Int?` | Gray button — focus background |
| `grayBackgroundDisabled` | `@ColorInt Int?` | Gray button — disabled background |
| `grayText` | `@ColorInt Int?` | Gray button — text |
| `grayTextHover` | `@ColorInt Int?` | Gray button — hover text |
| `grayTextFocus` | `@ColorInt Int?` | Gray button — focus text |
| `grayTextDisabled` | `@ColorInt Int?` | Gray button — disabled text |
| `grayLoaderColor` | `@ColorInt Int?` | Gray button — loader |
| `grayLoaderColorDisabled` | `@ColorInt Int?` | Gray button — disabled loader |
| `grayFocusOutlineColor` | `@ColorInt Int?` | Gray button — focus outline |
| `mutedBackground` | `@ColorInt Int?` | Muted button — default background |
| `mutedBackgroundHover` | `@ColorInt Int?` | Muted button — hover background |
| `mutedBackgroundFocus` | `@ColorInt Int?` | Muted button — focus background |
| `mutedBackgroundDisabled` | `@ColorInt Int?` | Muted button — disabled background |
| `mutedText` | `@ColorInt Int?` | Muted button — text |
| `mutedTextHover` | `@ColorInt Int?` | Muted button — hover text |
| `mutedTextFocus` | `@ColorInt Int?` | Muted button — focus text |
| `mutedTextDisabled` | `@ColorInt Int?` | Muted button — disabled text |
| `mutedLoaderColor` | `@ColorInt Int?` | Muted button — loader |
| `mutedLoaderColorDisabled` | `@ColorInt Int?` | Muted button — disabled loader |
| `mutedFocusOutlineColor` | `@ColorInt Int?` | Muted button — focus outline |
| `transparentBackground` | `@ColorInt Int?` | Transparent button — default background |
| `transparentBackgroundHover` | `@ColorInt Int?` | Transparent button — hover background |
| `transparentBackgroundFocus` | `@ColorInt Int?` | Transparent button — focus background |
| `transparentBackgroundDisabled` | `@ColorInt Int?` | Transparent button — disabled background |
| `transparentText` | `@ColorInt Int?` | Transparent button — text |
| `transparentTextHover` | `@ColorInt Int?` | Transparent button — hover text |
| `transparentTextFocus` | `@ColorInt Int?` | Transparent button — focus text |
| `transparentTextDisabled` | `@ColorInt Int?` | Transparent button — disabled text |
| `transparentLoaderColor` | `@ColorInt Int?` | Transparent button — loader |
| `transparentLoaderColorDisabled` | `@ColorInt Int?` | Transparent button — disabled loader |
| `transparentFocusOutlineColor` | `@ColorInt Int?` | Transparent button — focus outline |
| `transparentWhiteBackground` | `@ColorInt Int?` | Transparent White button — default background |
| `transparentWhiteBackgroundHover` | `@ColorInt Int?` | Transparent White button — hover background |
| `transparentWhiteBackgroundFocus` | `@ColorInt Int?` | Transparent White button — focus background |
| `transparentWhiteBackgroundDisabled` | `@ColorInt Int?` | Transparent White button — disabled background |
| `transparentWhiteText` | `@ColorInt Int?` | Transparent White button — text |
| `transparentWhiteTextHover` | `@ColorInt Int?` | Transparent White button — hover text |
| `transparentWhiteTextFocus` | `@ColorInt Int?` | Transparent White button — focus text |
| `transparentWhiteTextDisabled` | `@ColorInt Int?` | Transparent White button — disabled text |
| `transparentWhiteLoaderColor` | `@ColorInt Int?` | Transparent White button — loader |
| `transparentWhiteLoaderColorDisabled` | `@ColorInt Int?` | Transparent White button — disabled loader |
| `transparentWhiteFocusOutlineColor` | `@ColorInt Int?` | Transparent White button — focus outline |
| `errorMutedBackground` | `@ColorInt Int?` | Error Muted button — default background |
| `errorMutedBackgroundHover` | `@ColorInt Int?` | Error Muted button — hover background |
| `errorMutedBackgroundFocus` | `@ColorInt Int?` | Error Muted button — focus background |
| `errorMutedBackgroundDisabled` | `@ColorInt Int?` | Error Muted button — disabled background |
| `errorMutedText` | `@ColorInt Int?` | Error Muted button — text |
| `errorMutedTextHover` | `@ColorInt Int?` | Error Muted button — hover text |
| `errorMutedTextFocus` | `@ColorInt Int?` | Error Muted button — focus text |
| `errorMutedTextDisabled` | `@ColorInt Int?` | Error Muted button — disabled text |
| `errorMutedLoaderColor` | `@ColorInt Int?` | Error Muted button — loader |
| `errorMutedLoaderColorDisabled` | `@ColorInt Int?` | Error Muted button — disabled loader |
| `errorMutedFocusOutlineColor` | `@ColorInt Int?` | Error Muted button — focus outline |
| `errorBackground` | `@ColorInt Int?` | Error button — default background |
| `errorBackgroundHover` | `@ColorInt Int?` | Error button — hover background |
| `errorBackgroundFocus` | `@ColorInt Int?` | Error button — focus background |
| `errorBackgroundDisabled` | `@ColorInt Int?` | Error button — disabled background |
| `errorText` | `@ColorInt Int?` | Error button — text |
| `errorTextHover` | `@ColorInt Int?` | Error button — hover text |
| `errorTextFocus` | `@ColorInt Int?` | Error button — focus text |
| `errorTextDisabled` | `@ColorInt Int?` | Error button — disabled text |
| `errorLoaderColor` | `@ColorInt Int?` | Error button — loader |
| `errorLoaderColorDisabled` | `@ColorInt Int?` | Error button — disabled loader |
| `errorFocusOutlineColor` | `@ColorInt Int?` | Error button — focus outline |
| `warningMutedBackground` | `@ColorInt Int?` | Warning Muted button — default background |
| `warningMutedBackgroundHover` | `@ColorInt Int?` | Warning Muted button — hover background |
| `warningMutedBackgroundFocus` | `@ColorInt Int?` | Warning Muted button — focus background |
| `warningMutedBackgroundDisabled` | `@ColorInt Int?` | Warning Muted button — disabled background |
| `warningMutedText` | `@ColorInt Int?` | Warning Muted button — text |
| `warningMutedTextHover` | `@ColorInt Int?` | Warning Muted button — hover text |
| `warningMutedTextFocus` | `@ColorInt Int?` | Warning Muted button — focus text |
| `warningMutedTextDisabled` | `@ColorInt Int?` | Warning Muted button — disabled text |
| `warningMutedLoaderColor` | `@ColorInt Int?` | Warning Muted button — loader |
| `warningMutedLoaderColorDisabled` | `@ColorInt Int?` | Warning Muted button — disabled loader |
| `warningMutedFocusOutlineColor` | `@ColorInt Int?` | Warning Muted button — focus outline |
| `warningBackground` | `@ColorInt Int?` | Warning button — default background |
| `warningBackgroundHover` | `@ColorInt Int?` | Warning button — hover background |
| `warningBackgroundFocus` | `@ColorInt Int?` | Warning button — focus background |
| `warningBackgroundDisabled` | `@ColorInt Int?` | Warning button — disabled background |
| `warningText` | `@ColorInt Int?` | Warning button — text |
| `warningTextHover` | `@ColorInt Int?` | Warning button — hover text |
| `warningTextFocus` | `@ColorInt Int?` | Warning button — focus text |
| `warningTextDisabled` | `@ColorInt Int?` | Warning button — disabled text |
| `warningLoaderColor` | `@ColorInt Int?` | Warning button — loader |
| `warningLoaderColorDisabled` | `@ColorInt Int?` | Warning button — disabled loader |
| `warningFocusOutlineColor` | `@ColorInt Int?` | Warning button — focus outline |
| `whiteBackground` | `@ColorInt Int?` | White button — default background |
| `whiteBackgroundHover` | `@ColorInt Int?` | White button — hover background |
| `whiteBackgroundFocus` | `@ColorInt Int?` | White button — focus background |
| `whiteBackgroundDisabled` | `@ColorInt Int?` | White button — disabled background |
| `whiteText` | `@ColorInt Int?` | White button — text |
| `whiteTextHover` | `@ColorInt Int?` | White button — hover text |
| `whiteTextFocus` | `@ColorInt Int?` | White button — focus text |
| `whiteTextDisabled` | `@ColorInt Int?` | White button — disabled text |
| `whiteLoaderColor` | `@ColorInt Int?` | White button — loader |
| `whiteLoaderColorDisabled` | `@ColorInt Int?` | White button — disabled loader |
| `whiteFocusOutlineColor` | `@ColorInt Int?` | White button — focus outline |

### `AstroButtonIconStyle`

Wrapper for the `buttonsIcon` slot. Both fields optional.

| Field        | Type                          | Description                                                                |
|--------------|-------------------------------|----------------------------------------------------------------------------|
| `colors`     | `AstroButtonIconColors?`      | 132 tokens (see `colors:` table below).                                     |
| `typography` | `AstroButtonIconTypography?`  | Single `label` slot of type [`AstroFontStyle?`](#astrofontstyle) for the text variant of icon-with-text buttons. |

#### `colors:` → `AstroButtonIconColors`

Icon buttons mirror the 12 button variants but expose an icon-specific prop set (icon color, text, loader, focus outline). 132 tokens total. Every field is an optional `@ColorInt Int?` — see [Color values](#color-values).

| Field | Type | Description |
|-------|------|-------------|
| `primaryBackground` | `@ColorInt Int?` | Primary icon button — default background |
| `primaryBackgroundHover` | `@ColorInt Int?` | Primary icon button — hover background |
| `primaryBackgroundFocus` | `@ColorInt Int?` | Primary icon button — focus background |
| `primaryBackgroundDisabled` | `@ColorInt Int?` | Primary icon button — disabled background |
| `primaryIconColor` | `@ColorInt Int?` | Primary icon button — icon |
| `primaryIconColorDisabled` | `@ColorInt Int?` | Primary icon button — disabled icon |
| `primaryText` | `@ColorInt Int?` | Primary icon button — text |
| `primaryTextDisabled` | `@ColorInt Int?` | Primary icon button — disabled text |
| `primaryLoaderColor` | `@ColorInt Int?` | Primary icon button — loader |
| `primaryLoaderColorDisabled` | `@ColorInt Int?` | Primary icon button — disabled loader |
| `primaryFocusOutlineColor` | `@ColorInt Int?` | Primary icon button — focus outline |
| `secondaryBackground` | `@ColorInt Int?` | Secondary icon button — default background |
| `secondaryBackgroundHover` | `@ColorInt Int?` | Secondary icon button — hover background |
| `secondaryBackgroundFocus` | `@ColorInt Int?` | Secondary icon button — focus background |
| `secondaryBackgroundDisabled` | `@ColorInt Int?` | Secondary icon button — disabled background |
| `secondaryIconColor` | `@ColorInt Int?` | Secondary icon button — icon |
| `secondaryIconColorDisabled` | `@ColorInt Int?` | Secondary icon button — disabled icon |
| `secondaryText` | `@ColorInt Int?` | Secondary icon button — text |
| `secondaryTextDisabled` | `@ColorInt Int?` | Secondary icon button — disabled text |
| `secondaryLoaderColor` | `@ColorInt Int?` | Secondary icon button — loader |
| `secondaryLoaderColorDisabled` | `@ColorInt Int?` | Secondary icon button — disabled loader |
| `secondaryFocusOutlineColor` | `@ColorInt Int?` | Secondary icon button — focus outline |
| `tertiaryBackground` | `@ColorInt Int?` | Tertiary icon button — default background |
| `tertiaryBackgroundHover` | `@ColorInt Int?` | Tertiary icon button — hover background |
| `tertiaryBackgroundFocus` | `@ColorInt Int?` | Tertiary icon button — focus background |
| `tertiaryBackgroundDisabled` | `@ColorInt Int?` | Tertiary icon button — disabled background |
| `tertiaryIconColor` | `@ColorInt Int?` | Tertiary icon button — icon |
| `tertiaryIconColorDisabled` | `@ColorInt Int?` | Tertiary icon button — disabled icon |
| `tertiaryText` | `@ColorInt Int?` | Tertiary icon button — text |
| `tertiaryTextDisabled` | `@ColorInt Int?` | Tertiary icon button — disabled text |
| `tertiaryLoaderColor` | `@ColorInt Int?` | Tertiary icon button — loader |
| `tertiaryLoaderColorDisabled` | `@ColorInt Int?` | Tertiary icon button — disabled loader |
| `tertiaryFocusOutlineColor` | `@ColorInt Int?` | Tertiary icon button — focus outline |
| `grayBackground` | `@ColorInt Int?` | Gray icon button — default background |
| `grayBackgroundHover` | `@ColorInt Int?` | Gray icon button — hover background |
| `grayBackgroundFocus` | `@ColorInt Int?` | Gray icon button — focus background |
| `grayBackgroundDisabled` | `@ColorInt Int?` | Gray icon button — disabled background |
| `grayIconColor` | `@ColorInt Int?` | Gray icon button — icon |
| `grayIconColorDisabled` | `@ColorInt Int?` | Gray icon button — disabled icon |
| `grayText` | `@ColorInt Int?` | Gray icon button — text |
| `grayTextDisabled` | `@ColorInt Int?` | Gray icon button — disabled text |
| `grayLoaderColor` | `@ColorInt Int?` | Gray icon button — loader |
| `grayLoaderColorDisabled` | `@ColorInt Int?` | Gray icon button — disabled loader |
| `grayFocusOutlineColor` | `@ColorInt Int?` | Gray icon button — focus outline |
| `mutedBackground` | `@ColorInt Int?` | Muted icon button — default background |
| `mutedBackgroundHover` | `@ColorInt Int?` | Muted icon button — hover background |
| `mutedBackgroundFocus` | `@ColorInt Int?` | Muted icon button — focus background |
| `mutedBackgroundDisabled` | `@ColorInt Int?` | Muted icon button — disabled background |
| `mutedIconColor` | `@ColorInt Int?` | Muted icon button — icon |
| `mutedIconColorDisabled` | `@ColorInt Int?` | Muted icon button — disabled icon |
| `mutedText` | `@ColorInt Int?` | Muted icon button — text |
| `mutedTextDisabled` | `@ColorInt Int?` | Muted icon button — disabled text |
| `mutedLoaderColor` | `@ColorInt Int?` | Muted icon button — loader |
| `mutedLoaderColorDisabled` | `@ColorInt Int?` | Muted icon button — disabled loader |
| `mutedFocusOutlineColor` | `@ColorInt Int?` | Muted icon button — focus outline |
| `transparentBackground` | `@ColorInt Int?` | Transparent icon button — default background |
| `transparentBackgroundHover` | `@ColorInt Int?` | Transparent icon button — hover background |
| `transparentBackgroundFocus` | `@ColorInt Int?` | Transparent icon button — focus background |
| `transparentBackgroundDisabled` | `@ColorInt Int?` | Transparent icon button — disabled background |
| `transparentIconColor` | `@ColorInt Int?` | Transparent icon button — icon |
| `transparentIconColorDisabled` | `@ColorInt Int?` | Transparent icon button — disabled icon |
| `transparentText` | `@ColorInt Int?` | Transparent icon button — text |
| `transparentTextDisabled` | `@ColorInt Int?` | Transparent icon button — disabled text |
| `transparentLoaderColor` | `@ColorInt Int?` | Transparent icon button — loader |
| `transparentLoaderColorDisabled` | `@ColorInt Int?` | Transparent icon button — disabled loader |
| `transparentFocusOutlineColor` | `@ColorInt Int?` | Transparent icon button — focus outline |
| `transparentWhiteBackground` | `@ColorInt Int?` | Transparent White icon button — default background |
| `transparentWhiteBackgroundHover` | `@ColorInt Int?` | Transparent White icon button — hover background |
| `transparentWhiteBackgroundFocus` | `@ColorInt Int?` | Transparent White icon button — focus background |
| `transparentWhiteBackgroundDisabled` | `@ColorInt Int?` | Transparent White icon button — disabled background |
| `transparentWhiteIconColor` | `@ColorInt Int?` | Transparent White icon button — icon |
| `transparentWhiteIconColorDisabled` | `@ColorInt Int?` | Transparent White icon button — disabled icon |
| `transparentWhiteText` | `@ColorInt Int?` | Transparent White icon button — text |
| `transparentWhiteTextDisabled` | `@ColorInt Int?` | Transparent White icon button — disabled text |
| `transparentWhiteLoaderColor` | `@ColorInt Int?` | Transparent White icon button — loader |
| `transparentWhiteLoaderColorDisabled` | `@ColorInt Int?` | Transparent White icon button — disabled loader |
| `transparentWhiteFocusOutlineColor` | `@ColorInt Int?` | Transparent White icon button — focus outline |
| `errorMutedBackground` | `@ColorInt Int?` | Error Muted icon button — default background |
| `errorMutedBackgroundHover` | `@ColorInt Int?` | Error Muted icon button — hover background |
| `errorMutedBackgroundFocus` | `@ColorInt Int?` | Error Muted icon button — focus background |
| `errorMutedBackgroundDisabled` | `@ColorInt Int?` | Error Muted icon button — disabled background |
| `errorMutedIconColor` | `@ColorInt Int?` | Error Muted icon button — icon |
| `errorMutedIconColorDisabled` | `@ColorInt Int?` | Error Muted icon button — disabled icon |
| `errorMutedText` | `@ColorInt Int?` | Error Muted icon button — text |
| `errorMutedTextDisabled` | `@ColorInt Int?` | Error Muted icon button — disabled text |
| `errorMutedLoaderColor` | `@ColorInt Int?` | Error Muted icon button — loader |
| `errorMutedLoaderColorDisabled` | `@ColorInt Int?` | Error Muted icon button — disabled loader |
| `errorMutedFocusOutlineColor` | `@ColorInt Int?` | Error Muted icon button — focus outline |
| `errorBackground` | `@ColorInt Int?` | Error icon button — default background |
| `errorBackgroundHover` | `@ColorInt Int?` | Error icon button — hover background |
| `errorBackgroundFocus` | `@ColorInt Int?` | Error icon button — focus background |
| `errorBackgroundDisabled` | `@ColorInt Int?` | Error icon button — disabled background |
| `errorIconColor` | `@ColorInt Int?` | Error icon button — icon |
| `errorIconColorDisabled` | `@ColorInt Int?` | Error icon button — disabled icon |
| `errorText` | `@ColorInt Int?` | Error icon button — text |
| `errorTextDisabled` | `@ColorInt Int?` | Error icon button — disabled text |
| `errorLoaderColor` | `@ColorInt Int?` | Error icon button — loader |
| `errorLoaderColorDisabled` | `@ColorInt Int?` | Error icon button — disabled loader |
| `errorFocusOutlineColor` | `@ColorInt Int?` | Error icon button — focus outline |
| `warningMutedBackground` | `@ColorInt Int?` | Warning Muted icon button — default background |
| `warningMutedBackgroundHover` | `@ColorInt Int?` | Warning Muted icon button — hover background |
| `warningMutedBackgroundFocus` | `@ColorInt Int?` | Warning Muted icon button — focus background |
| `warningMutedBackgroundDisabled` | `@ColorInt Int?` | Warning Muted icon button — disabled background |
| `warningMutedIconColor` | `@ColorInt Int?` | Warning Muted icon button — icon |
| `warningMutedIconColorDisabled` | `@ColorInt Int?` | Warning Muted icon button — disabled icon |
| `warningMutedText` | `@ColorInt Int?` | Warning Muted icon button — text |
| `warningMutedTextDisabled` | `@ColorInt Int?` | Warning Muted icon button — disabled text |
| `warningMutedLoaderColor` | `@ColorInt Int?` | Warning Muted icon button — loader |
| `warningMutedLoaderColorDisabled` | `@ColorInt Int?` | Warning Muted icon button — disabled loader |
| `warningMutedFocusOutlineColor` | `@ColorInt Int?` | Warning Muted icon button — focus outline |
| `warningBackground` | `@ColorInt Int?` | Warning icon button — default background |
| `warningBackgroundHover` | `@ColorInt Int?` | Warning icon button — hover background |
| `warningBackgroundFocus` | `@ColorInt Int?` | Warning icon button — focus background |
| `warningBackgroundDisabled` | `@ColorInt Int?` | Warning icon button — disabled background |
| `warningIconColor` | `@ColorInt Int?` | Warning icon button — icon |
| `warningIconColorDisabled` | `@ColorInt Int?` | Warning icon button — disabled icon |
| `warningText` | `@ColorInt Int?` | Warning icon button — text |
| `warningTextDisabled` | `@ColorInt Int?` | Warning icon button — disabled text |
| `warningLoaderColor` | `@ColorInt Int?` | Warning icon button — loader |
| `warningLoaderColorDisabled` | `@ColorInt Int?` | Warning icon button — disabled loader |
| `warningFocusOutlineColor` | `@ColorInt Int?` | Warning icon button — focus outline |
| `whiteBackground` | `@ColorInt Int?` | White icon button — default background |
| `whiteBackgroundHover` | `@ColorInt Int?` | White icon button — hover background |
| `whiteBackgroundFocus` | `@ColorInt Int?` | White icon button — focus background |
| `whiteBackgroundDisabled` | `@ColorInt Int?` | White icon button — disabled background |
| `whiteIconColor` | `@ColorInt Int?` | White icon button — icon |
| `whiteIconColorDisabled` | `@ColorInt Int?` | White icon button — disabled icon |
| `whiteText` | `@ColorInt Int?` | White icon button — text |
| `whiteTextDisabled` | `@ColorInt Int?` | White icon button — disabled text |
| `whiteLoaderColor` | `@ColorInt Int?` | White icon button — loader |
| `whiteLoaderColorDisabled` | `@ColorInt Int?` | White icon button — disabled loader |
| `whiteFocusOutlineColor` | `@ColorInt Int?` | White icon button — focus outline |

### `AstroButtonPillStyle`

Wrapper for the `buttonsPill` slot. Both fields optional.

| Field        | Type                          | Description                                                                |
|--------------|-------------------------------|----------------------------------------------------------------------------|
| `colors`     | `AstroButtonPillColors?`      | 70 tokens (see `colors:` table below).                                      |
| `typography` | `AstroButtonPillTypography?`  | Single `label` slot of type [`AstroFontStyle?`](#astrofontstyle) for pill text. |

#### `colors:` → `AstroButtonPillColors`

Pills cover 14 semantic statuses × 5 props (background, hover background, border, text, focus outline) = 70 tokens. Every field is an optional `@ColorInt Int?` — see [Color values](#color-values).

| Field | Type | Description |
|-------|------|-------------|
| `infoBackground` | `@ColorInt Int?` | Info pill — default background |
| `infoBackgroundHover` | `@ColorInt Int?` | Info pill — hover background |
| `infoBorder` | `@ColorInt Int?` | Info pill — border |
| `infoText` | `@ColorInt Int?` | Info pill — text |
| `infoFocusOutlineColor` | `@ColorInt Int?` | Info pill — focus outline |
| `infoAltBackground` | `@ColorInt Int?` | Info Alt pill — default background |
| `infoAltBackgroundHover` | `@ColorInt Int?` | Info Alt pill — hover background |
| `infoAltBorder` | `@ColorInt Int?` | Info Alt pill — border |
| `infoAltText` | `@ColorInt Int?` | Info Alt pill — text |
| `infoAltFocusOutlineColor` | `@ColorInt Int?` | Info Alt pill — focus outline |
| `successBackground` | `@ColorInt Int?` | Success pill — default background |
| `successBackgroundHover` | `@ColorInt Int?` | Success pill — hover background |
| `successBorder` | `@ColorInt Int?` | Success pill — border |
| `successText` | `@ColorInt Int?` | Success pill — text |
| `successFocusOutlineColor` | `@ColorInt Int?` | Success pill — focus outline |
| `successMutedBackground` | `@ColorInt Int?` | Success Muted pill — default background |
| `successMutedBackgroundHover` | `@ColorInt Int?` | Success Muted pill — hover background |
| `successMutedBorder` | `@ColorInt Int?` | Success Muted pill — border |
| `successMutedText` | `@ColorInt Int?` | Success Muted pill — text |
| `successMutedFocusOutlineColor` | `@ColorInt Int?` | Success Muted pill — focus outline |
| `errorBackground` | `@ColorInt Int?` | Error pill — default background |
| `errorBackgroundHover` | `@ColorInt Int?` | Error pill — hover background |
| `errorBorder` | `@ColorInt Int?` | Error pill — border |
| `errorText` | `@ColorInt Int?` | Error pill — text |
| `errorFocusOutlineColor` | `@ColorInt Int?` | Error pill — focus outline |
| `errorMutedBackground` | `@ColorInt Int?` | Error Muted pill — default background |
| `errorMutedBackgroundHover` | `@ColorInt Int?` | Error Muted pill — hover background |
| `errorMutedBorder` | `@ColorInt Int?` | Error Muted pill — border |
| `errorMutedText` | `@ColorInt Int?` | Error Muted pill — text |
| `errorMutedFocusOutlineColor` | `@ColorInt Int?` | Error Muted pill — focus outline |
| `warningBackground` | `@ColorInt Int?` | Warning pill — default background |
| `warningBackgroundHover` | `@ColorInt Int?` | Warning pill — hover background |
| `warningBorder` | `@ColorInt Int?` | Warning pill — border |
| `warningText` | `@ColorInt Int?` | Warning pill — text |
| `warningFocusOutlineColor` | `@ColorInt Int?` | Warning pill — focus outline |
| `warningMutedBackground` | `@ColorInt Int?` | Warning Muted pill — default background |
| `warningMutedBackgroundHover` | `@ColorInt Int?` | Warning Muted pill — hover background |
| `warningMutedBorder` | `@ColorInt Int?` | Warning Muted pill — border |
| `warningMutedText` | `@ColorInt Int?` | Warning Muted pill — text |
| `warningMutedFocusOutlineColor` | `@ColorInt Int?` | Warning Muted pill — focus outline |
| `neutralBackground` | `@ColorInt Int?` | Neutral pill — default background |
| `neutralBackgroundHover` | `@ColorInt Int?` | Neutral pill — hover background |
| `neutralBorder` | `@ColorInt Int?` | Neutral pill — border |
| `neutralText` | `@ColorInt Int?` | Neutral pill — text |
| `neutralFocusOutlineColor` | `@ColorInt Int?` | Neutral pill — focus outline |
| `neutralMutedBackground` | `@ColorInt Int?` | Neutral Muted pill — default background |
| `neutralMutedBackgroundHover` | `@ColorInt Int?` | Neutral Muted pill — hover background |
| `neutralMutedBorder` | `@ColorInt Int?` | Neutral Muted pill — border |
| `neutralMutedText` | `@ColorInt Int?` | Neutral Muted pill — text |
| `neutralMutedFocusOutlineColor` | `@ColorInt Int?` | Neutral Muted pill — focus outline |
| `pearlBackground` | `@ColorInt Int?` | Pearl pill — default background |
| `pearlBackgroundHover` | `@ColorInt Int?` | Pearl pill — hover background |
| `pearlBorder` | `@ColorInt Int?` | Pearl pill — border |
| `pearlText` | `@ColorInt Int?` | Pearl pill — text |
| `pearlFocusOutlineColor` | `@ColorInt Int?` | Pearl pill — focus outline |
| `pearlMutedBackground` | `@ColorInt Int?` | Pearl Muted pill — default background |
| `pearlMutedBackgroundHover` | `@ColorInt Int?` | Pearl Muted pill — hover background |
| `pearlMutedBorder` | `@ColorInt Int?` | Pearl Muted pill — border |
| `pearlMutedText` | `@ColorInt Int?` | Pearl Muted pill — text |
| `pearlMutedFocusOutlineColor` | `@ColorInt Int?` | Pearl Muted pill — focus outline |
| `highlightBackground` | `@ColorInt Int?` | Highlight pill — default background |
| `highlightBackgroundHover` | `@ColorInt Int?` | Highlight pill — hover background |
| `highlightBorder` | `@ColorInt Int?` | Highlight pill — border |
| `highlightText` | `@ColorInt Int?` | Highlight pill — text |
| `highlightFocusOutlineColor` | `@ColorInt Int?` | Highlight pill — focus outline |
| `highlightMutedBackground` | `@ColorInt Int?` | Highlight Muted pill — default background |
| `highlightMutedBackgroundHover` | `@ColorInt Int?` | Highlight Muted pill — hover background |
| `highlightMutedBorder` | `@ColorInt Int?` | Highlight Muted pill — border |
| `highlightMutedText` | `@ColorInt Int?` | Highlight Muted pill — text |
| `highlightMutedFocusOutlineColor` | `@ColorInt Int?` | Highlight Muted pill — focus outline |

### `AstroInputStyle`

Wrapper for the `inputs` slot. Both fields optional.

| Field        | Type                       | Description                                                                |
|--------------|----------------------------|----------------------------------------------------------------------------|
| `colors`     | `AstroInputColors?`        | 55 tokens (see `colors:` table below).                                      |
| `typography` | `AstroInputTypography?`    | Per-slot typography: `input` (typed value), `label` (floating/static label), `helper` (helper / error / success message), `placeholder`. Each is an [`AstroFontStyle?`](#astrofontstyle). |

#### `colors:` → `AstroInputColors`

Tokens for text inputs, including the trailing icon area, the dropdown panel, and the phone-country dropdown (sheet + items). 55 tokens. Every field is an optional `@ColorInt Int?` — see [Color values](#color-values).

| Field | Type | Description |
|-------|------|-------------|
| `background` | `@ColorInt Int?` | Input background (default) |
| `backgroundHover` | `@ColorInt Int?` | Input background (hover) |
| `backgroundFocus` | `@ColorInt Int?` | Input background (focus) |
| `backgroundActive` | `@ColorInt Int?` | Input background (active/pressed) |
| `backgroundFilled` | `@ColorInt Int?` | Input background (filled) |
| `backgroundDisabled` | `@ColorInt Int?` | Input background (disabled) |
| `backgroundError` | `@ColorInt Int?` | Input background (error) |
| `backgroundSuccess` | `@ColorInt Int?` | Input background (success) |
| `border` | `@ColorInt Int?` | Input border (default) |
| `borderHover` | `@ColorInt Int?` | Input border (hover) |
| `borderFocus` | `@ColorInt Int?` | Input border (focus) |
| `borderActive` | `@ColorInt Int?` | Input border (active) |
| `borderFilled` | `@ColorInt Int?` | Input border (filled) |
| `borderDisabled` | `@ColorInt Int?` | Input border (disabled) |
| `borderError` | `@ColorInt Int?` | Input border (error) |
| `borderSuccess` | `@ColorInt Int?` | Input border (success) |
| `text` | `@ColorInt Int?` | Input text (default) |
| `textHover` | `@ColorInt Int?` | Input text (hover) |
| `textDisabled` | `@ColorInt Int?` | Input text (disabled) |
| `placeholder` | `@ColorInt Int?` | Placeholder text |
| `placeholderHover` | `@ColorInt Int?` | Placeholder text (hover) |
| `label` | `@ColorInt Int?` | Field label |
| `helper` | `@ColorInt Int?` | Helper text |
| `errorMessage` | `@ColorInt Int?` | Error message text |
| `successMessage` | `@ColorInt Int?` | Success message text |
| `icon` | `@ColorInt Int?` | Leading/trailing icon |
| `iconHover` | `@ColorInt Int?` | Icon (hover) |
| `iconFocus` | `@ColorInt Int?` | Icon (focus) |
| `iconDisabled` | `@ColorInt Int?` | Icon (disabled) |
| `caret` | `@ColorInt Int?` | Caret/cursor color |
| `focusOutlineColor` | `@ColorInt Int?` | Focus outline ring |
| `iconBackground` | `@ColorInt Int?` | Icon background (default) |
| `iconBackgroundHover` | `@ColorInt Int?` | Icon background (hover) |
| `iconBackgroundFocus` | `@ColorInt Int?` | Icon background (focus) |
| `iconBackgroundDisabled` | `@ColorInt Int?` | Icon background (disabled) |
| `dropdownBackground` | `@ColorInt Int?` | Dropdown panel background |
| `dropdownBorder` | `@ColorInt Int?` | Dropdown panel border |
| `dropdownOptionBackground` | `@ColorInt Int?` | Dropdown option background |
| `dropdownOptionBackgroundHover` | `@ColorInt Int?` | Dropdown option background (hover) |
| `dropdownOptionBackgroundSelected` | `@ColorInt Int?` | Dropdown option background (selected) |
| `dropdownOptionBackgroundDisabled` | `@ColorInt Int?` | Dropdown option background (disabled) |
| `dropdownOptionText` | `@ColorInt Int?` | Dropdown option text |
| `dropdownOptionTextHover` | `@ColorInt Int?` | Dropdown option text (hover) |
| `dropdownOptionTextSelected` | `@ColorInt Int?` | Dropdown option text (selected) |
| `dropdownOptionTextDisabled` | `@ColorInt Int?` | Dropdown option text (disabled) |
| `dropdownOptionIcon` | `@ColorInt Int?` | Dropdown option icon |
| `dropdownEmptyMessage` | `@ColorInt Int?` | Dropdown empty-state text |
| `phoneDropdownBackground` | `@ColorInt Int?` | Phone country dropdown background |
| `phoneDropdownOverlayBackground` | `@ColorInt Int?` | Phone dropdown overlay background |
| `phoneDropdownHeaderBackground` | `@ColorInt Int?` | Phone dropdown header background |
| `phoneDropdownItemBackground` | `@ColorInt Int?` | Phone dropdown item background |
| `phoneDropdownItemBackgroundHover` | `@ColorInt Int?` | Phone dropdown item background (hover) |
| `phoneDropdownItemBackgroundActive` | `@ColorInt Int?` | Phone dropdown item background (active) |
| `phoneDropdownItemText` | `@ColorInt Int?` | Phone dropdown item text |
| `phoneDropdownItemTextSecondary` | `@ColorInt Int?` | Phone dropdown item text (secondary) |

### `AstroTypography`

Global typography settings. Exposes a single field — `fontFamily` — used as the default font family for every text token rendered by the SDK. Per-token typography customization (overriding `displayLarge`, `base500`, etc. individually) is **not** supported at the global typography level; use per-component typography slots (see [`AstroButtonStyle`](#astrobuttonstyle), [`AstroInputStyle`](#astroinputstyle), etc.) to customize specific component text.

#### Global default

| Field        | Type      | Description |
|--------------|-----------|-------------|
| `fontFamily` | `String?` | Optional global default font family applied to every text token rendered by the SDK. When omitted, the SDK's static default is used. The partner is responsible for ensuring the requested family is registered in the host app and resolvable when the SDK renders; the SDK does not load fonts on your behalf. `fontFamily` is not validated — any non-null string is forwarded as-is; an empty string is dropped. |

### `AstroFontStyle`

Leaf type used by per-component typography slots (`button.typography.label`, `input.typography.helper`, etc.). All fields are optional — only the fields you set are applied; the remaining attributes fall back to the SDK default.

| Field           | Type      | Accepted values                                                  |
|-----------------|-----------|------------------------------------------------------------------|
| `fontSize`      | `Float?`  | Non-negative number (size in px).                                |
| `fontWeight`    | `Int?`    | Integer in the range `100..900` (matches CSS / Material weights).|
| `lineHeight`    | `Float?`  | Non-negative number (line height in px).                         |
| `letterSpacing` | `Float?`  | Any number (px). May be negative for tighter tracking.           |
| `fontFamily`    | `String?` | Any non-null string accepted (no validation); an empty string is dropped. Applied to the specific component slot. The partner is responsible for ensuring the requested family is registered in the host app and resolvable when the SDK renders; the SDK does not load fonts on your behalf. |

Invalid values (negative `fontSize`/`lineHeight`, `fontWeight` outside `100..900`) cause the typed `AstroStyle.validate()` to throw. `fontFamily` is not validated — any non-null string is forwarded as-is.

## Example

```kotlin
import com.astropay.connect.core.AstroBorderColors
import com.astropay.connect.core.AstroButtonColors
import com.astropay.connect.core.AstroButtonStyle
import com.astropay.connect.core.AstroButtonTypography
import com.astropay.connect.core.AstroFontStyle
import com.astropay.connect.core.AstroHeaderStyle
import com.astropay.connect.core.AstroStyle
import com.astropay.connect.core.AstroSurfaceColors
import com.astropay.connect.core.AstroTextColors
import com.astropay.connect.core.AstroTypography

val style = AstroStyle(
    backgroundColor = Color(0xFF041311).toArgb(),   // cascades to surface.base
    primaryColor = Color(0xFF00DBBF).toArgb(),      // cascades to surface.highlight, text.highlight, border.highlight
    typography = AstroTypography(
        fontFamily = "Inter",
    ),
    surface = AstroSurfaceColors(
        // Explicit overrides take precedence over the cascade.
        secondary = Color(0xFF0B2624).toArgb(),
        muted = Color(0xFF143834).toArgb(),
        // Alpha is supported — this overlay is rendered at 50% opacity.
        overlay = Color(0x80000000).toArgb(),
    ),
    text = AstroTextColors(
        base = Color.White.toArgb(),
        muted = Color(0xFFA6B5B3).toArgb(),
    ),
    border = AstroBorderColors(
        base = Color(0xFF1F4A45).toArgb(),
    ),
    buttons = AstroButtonStyle(
        colors = AstroButtonColors(
            // Text colors are not cascaded, only backgrounds.
            primaryText = Color.Black.toArgb(),
            primaryBackgroundDisabled = Color(0xFF1F4A45).toArgb(),
            primaryTextDisabled = Color(0xFF5C7A77).toArgb(),
        ),
        typography = AstroButtonTypography(
            label = AstroFontStyle(fontSize = 14f, fontWeight = 500),
        ),
    ),
    header = AstroHeaderStyle(
        backgroundColor = Color(0xFF061E1D).toArgb(),
        borderColor = Color(0xFF1F4A45).toArgb(),
        borderWidth = 1f,
    ),
)
```

> **Theme awareness:** When you override a color via `AstroStyle`, the SDK stops using the theme-based default for that token. If your app supports both light and dark modes, build the typed structs dynamically based on the current `Configuration.uiMode`.

## Free-form overrides via `styleOverrides`

`AstroConfiguration.styleOverrides` is a free-form `Map<String, Any>?` that mirrors the same key shape as the typed `AstroStyle`. It is intended for partners who need to set tokens dynamically (e.g. fetched from a remote theme service) or to reach tokens that have not yet been promoted to the typed catalog.

The map accepts the same keys as the typed API:

- Top-level colors (e.g. `"surfaceBase"`, `"primaryColor"`). Color leaves accept **either** a hex string — 6-digit `"#RRGGBB"` or 8-digit `"#RRGGBBAA"` for alpha, leading `#` optional — **or** an `androidx.compose.ui.graphics.Color` value. Both forms are normalized to the same hex wire representation, so mixing them within the same map is fine. Any malformed hex string causes `AstroConfiguration.validate()` to throw, so invalid hex is caught at configuration time rather than silently dropped at runtime; `Color` leaves are always considered valid. A raw `Int` is **not** accepted as a color value — in a `Map<String, Any>` it is indistinguishable from any other numeric value; wrap it with `Color(intValue)` (or use a hex string) to pass a literal color.
- The `typography` map, which accepts a single top-level `fontFamily` string — the global default font family, with the same semantics as the typed [`AstroTypography.fontFamily`](#astrotypography). Per-token nested keys (e.g. `typography.base500.fontFamily`, `typography.displayLarge.fontSize`) are **not** honored — they are silently ignored for forward compatibility, so partners migrating away from per-token overrides won't see validation errors but the values won't take effect either.
- Per-component typography under the component slot (e.g. `button.typography.label.fontSize`, `input.typography.placeholder.fontSize`) — fully supported.

Numeric values for `fontSize`, `lineHeight`, and `letterSpacing` are automatically normalized — you can pass either a number (e.g. `14`) or a string (e.g. `"14px"`); both work. Normalization recurses into nested maps, so numbers inside `button.typography.label`, `input.typography.helper`, and any other per-component typography map are normalized the same way. `fontWeight` is the only typography field that is **not** normalized to a `"<n>px"` string — it stays an integer in the range `100..900` because it is a unitless weight.

> The typed `AstroStyle` API uses `@ColorInt Int` for color fields; the free-form `styleOverrides` map accepts **both** hex strings and `androidx.compose.ui.graphics.Color` values for color leaves. Internally both are converted to the same hex wire representation.

```kotlin
// Form 1 — hex string
mapOf(
    "surfaceBase" to "#FFFFFF",
    "primaryColor" to "#0033CC80",
)

// Form 2 — Compose Color (equivalent on the wire)
mapOf(
    "surfaceBase" to Color.White,
    "primaryColor" to Color(0x800033CC),
)
```

```kotlin
val configuration = AstroConfiguration.builder()
    .setEnvironment("sandbox")
    .setAppIssuer("your-app-issuer")
    .setClientId("your-client-id")
    .setPartnerUserId("your-partner-user-id")
    .setAccessToken("your-access-token")
    .setStyleOverrides(mapOf(
        "surfaceBase" to "#FFFFFF",
        "typography" to mapOf(
            "fontFamily" to "Inter"
        ),
        "button" to mapOf(
            "typography" to mapOf(
                "label" to mapOf("fontSize" to 14, "fontWeight" to 500)
            )
        )
    ))
    .build()
```

> The typed API (`AstroStyle.typography.fontFamily`, per-component `typography` slots, etc.) is the recommended path because it gives compile-time safety and IDE autocompletion. `styleOverrides` is the escape hatch for the dynamic / late-bound use cases described above.

### Brand color aliases (apply throughout the SDK)

Two brand colors — background and primary — are **special-cased**: when set via `styleOverrides`, they apply throughout the SDK, including the initial loading screen background and the loading spinner. Every other `styleOverrides` key applies only to the main SDK content and does not affect the initial loading screen.

Each brand color accepts a top-level key in `styleOverrides`, plus a nested form under `colors`. When both are present, the top-level key takes precedence.

| Brand color  | Accepted keys                                                                                                          | Where it applies                  |
|--------------|---------------------------------------------------------------------------------------------------------------------------|-----------------------------------|
| Background   | `backgroundColor`, or nested `colors.surfaceBase`                                          | Initial loading screen background |
| Primary      | `primaryColor`, `surfaceHighlight`, or nested `colors.surfaceHighlight` / `colors.primaryColor` | Loading spinner color             |

**Precedence (highest → lowest):**

1. `styleOverrides` key — the top-level key takes precedence over the nested `colors.*` form.
2. Typed `AstroStyle.surface.base` (for background) / `AstroStyle.surface.highlight` (for primary) — the specific token.
3. Typed `AstroStyle.backgroundColor` / `AstroStyle.primaryColor` — the brand-level field.
4. Default theme color.

The specific surface token wins over the brand-level field when both are set — for example, `style.surface.base` overrides `style.backgroundColor` for the initial loading screen, mirroring the typed cascade semantics used elsewhere in the SDK. Free-form `styleOverrides` apply after the typed `style` cascade and the specific token wins over the brand-level field.

**Accepted value forms (brand color slots only):** `androidx.compose.ui.graphics.Color`, hex string (`#RRGGBB` / `#RRGGBBAA`, leading `#` optional), **or** a raw `@ColorInt Int`. Raw `Int` is accepted only for these brand color slots because the slot is by definition a color — there is no ambiguity. The free-form `styleOverrides` validation path for non-brand keys still rejects raw `Int` (wrap with `Color(intValue)` or use a hex string).

```kotlin
// Hex string form
val configuration = AstroConfiguration.builder()
    .setEnvironment("sandbox")
    .setAppIssuer("your-app-issuer")
    .setClientId("your-client-id")
    .setPartnerUserId("your-partner-user-id")
    .setAccessToken("your-access-token")
    .setStyleOverrides(mapOf(
        "backgroundColor" to "#041311",
        "primaryColor" to "#00DBBF",
    ))
    .build()

// Compose Color form (equivalent on the wire)
mapOf(
    "backgroundColor" to Color(0xFF041311),
    "primaryColor" to Color(0xFF00DBBF),
)

// Raw @ColorInt Int form — accepted only for these brand color slots
mapOf(
    "backgroundColor" to 0xFF041311.toInt(),
    "primaryColor" to 0xFF00DBBF.toInt(),
)
```

> If a brand color key is present in `styleOverrides`, it is used for the initial loading screen even when the typed `AstroStyle.backgroundColor` / `AstroStyle.primaryColor` is also set — `styleOverrides` wins. The typed `AstroStyle` field is used only when no `styleOverrides` key is present.

### Header colors (header bar only)

The SDK header bar's background and border colors can also be set via `styleOverrides`, using a nested `header` object that mirrors the typed `AstroStyle.header` shape:

| Header bar target    | styleOverrides key path                           |
|----------------------|---------------------------------------------------|
| Header background    | `styleOverrides["header"]["backgroundColor"]`     |
| Header border        | `styleOverrides["header"]["borderColor"]`         |

The keys are case-sensitive camelCase — no aliases. Unlike the brand colors above, these header keys affect the SDK's header bar only; they do not change other parts of the SDK.

**Precedence (highest → lowest):**

1. `styleOverrides["header"]["backgroundColor"]` / `["borderColor"]` — wins when present.
2. Typed `AstroStyle.header.backgroundColor` / `AstroStyle.header.borderColor`.
3. Default theme color.

**Accepted value forms:** a hex string (`#RRGGBB` / `#RRGGBBAA`, leading `#` optional) **or** an `androidx.compose.ui.graphics.Color` value. A malformed hex string causes `AstroConfiguration.validate()` to throw. The sibling layout keys under `header` (`borderWidth`, `paddingHorizontal`, `paddingVertical`) are not consumed from `styleOverrides` — set those on the typed `AstroStyle.header` instead.

```kotlin
// Hex string form
mapOf(
    "header" to mapOf(
        "backgroundColor" to "#FFFFFF",
        "borderColor" to "#000000",
    ),
)

// Compose Color form (equivalent on the wire)
mapOf(
    "header" to mapOf(
        "backgroundColor" to Color.White,
        "borderColor" to Color.Black,
    ),
)
```

> If a header color is present under `styleOverrides["header"]`, it is used for the native header even when the typed `AstroStyle.header.backgroundColor` / `borderColor` is also set — `styleOverrides` wins. The typed field is used only when no `styleOverrides` header key is present.
