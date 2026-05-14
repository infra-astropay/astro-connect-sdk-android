# SDK Events

Reference document for all events dispatched through Astro Connect SDK.

---

## Event Structure

| Field | Type | Description |
|-------|------|-------------|
| `screenName` | `string` | The current screen name where the event happened (see predefined screen names below) |
| `eventName` | `string` | Name of the event, should be unique and descriptive (e.g. `ob_step_email_btn_continue`) |
| `eventCategory` | `string` | One of the predefined categories (see below) that classifies the event type |
| `eventProperties` | `Record<string, any>` | Optional additional properties relevant to the event (e.g. `{ pep: true }` for legal step events) |
| `sessionId` | `string` | Unique ID generated per session (`uuid()`) |
| `appVersion` | `string` | Value of `REACT_APP_VERSION` |
| `platform` | `'ios' \| 'android' \| 'web'` | Current platform |

---

## Event Categories

| Category | Description |
|----------|-------------|
| `page_view` | A screen/page became visible to the user |
| `page_error_view` | A full-page error screen was displayed |
| `page_navigation` | A routing transition used to signal navigation between screens (not a user-visible screen) |
| `modal_view` | A modal/dialog was presented |
| `modal_error_view` | An error modal/dialog was presented |
| `user_action` | The user interacted with a UI element (button tap, toggle, click) |
| `validation` | A form or input validation passed successfully |
| `validation_error` | A form or input validation failed |

---

## Screen Names

### Onboarding

| Screen Name | Description |
|-------------|-------------|
| `ob_step_country_of_residence` | Country of residence selection step |
| `ob_step_email` | Email input step |
| `ob_step_email_validation_otp` | Email OTP verification step |
| `ob_step_personal_data` | Personal data form step |
| `ob_step_legal` | Legal declarations / PEP step |
| `ob_step_identity_validation` | Identity validation initial step |
| `ob_step_identity_validation_in_progress` | Identity validation in progress (waiting for result) |
| `ob_step_identity_validation_error` | Identity validation failed |
| `ob_step_identity_validation_success` | Identity validation succeeded |
| `ob_step_address` | Address input step |
| `ob_step_address_confirmation` | Address confirmation step |
| `ob_step_occupation` | Occupation selection step |
| `ob_step_financial_activity` | Financial activity declaration step |
| `ob_step_pin` | PIN creation step |
| `ob_step_pin_confirmation` | PIN confirmation step |
| `ob_step_trusted_device` | Trusted device registration step |

### Authentication

| Screen Name | Description |
|-------------|-------------|
| `auth_email_otp` | Sign in via email OTP |
| `auth_phone_otp` | Sign in via phone OTP |
| `auth_phone_input` | Phone number input for sign in |
| `auth_password` | Sign in via password |
| `auth_pin` | Sign in via PIN |
| `auth_otp` | Generic OTP screen (shared between email/phone) |
| `auth_face_match_intro` | Face match introduction/start screen |
| `auth_face_match_onfido` | Face match via Onfido provider |
| `auth_face_match_incode` | Face match via Incode provider |
| `auth_face_match_processing` | Face match processing/waiting screen |
| `auth_face_match_setup_trusted_device` | Trusted device setup after face match |
| `auth_face_match_rejected` | Face match was rejected |
| `auth_face_match_error` | Face match encountered an error |

### Home & Wallet

| Screen Name | Description |
|-------------|-------------|
| `home` | Main home screen |
| `add_funds_select_currency` | Currency selection for adding funds |
| `add_funds_select_amount` | Amount input for adding funds |
| `add_funds_select_method` | Payment method selection for adding funds |
| `add_funds_checkout_summary` | Checkout summary before confirming payment |

### Validation & Recovery

| Screen Name | Description |
|-------------|-------------|
| `pin_validation` | PIN validation screen (used for secure actions) |
| `pin_recovery_validation` | PIN recovery method routing/selection screen |
| `pin_recovery_otp` | OTP verification screen during PIN recovery |
| `pin_recovery_resolution` | PIN recovery resolution transition screen |
| `pin_recovery_creation` | New PIN creation screen during PIN recovery |
| `pin_recovery_confirmation` | New PIN confirmation screen during PIN recovery |
| `pin_recovery_face_match` | Face match screen during PIN recovery (fallback when no specific step is active) |
| `pin_recovery_face_match_intro` | Face match intro screen during PIN recovery |
| `pin_recovery_face_match_onfido` | Face match via Onfido during PIN recovery |
| `pin_recovery_face_match_incode` | Face match via Incode during PIN recovery |
| `pin_recovery_face_match_processing` | Face match processing/waiting screen during PIN recovery |
| `pin_recovery_face_match_rejected` | Face match rejected screen during PIN recovery |
| `pin_recovery_face_match_error` | Face match error screen during PIN recovery |
| `pin_recovery_face_match_setup_trusted_device` | Trusted device setup screen after face match during PIN recovery |
| `biometric_validation_error` | Biometric validation error screen |
| `kyc_validation` | KYC validation screen |

### Errors

| Screen Name | Description |
|-------------|-------------|
| `page_error` | Full-page error screen |
| `modal_error` | Error dialog/modal |

---

## Event Names

> The `Added` column indicates the date the event was introduced (ISO `YYYY-MM-DD`). Use `—` for legacy events whose introduction date predates this column.

### Onboarding — Country of Residence

| Event Name | Category | Description | Added |
|------------|----------|-------------|-------|
| `ob_step_country_of_residence_start` | `page_view` | Country of residence step became visible | — |
| `ob_step_country_of_residence_btn_continue` | `user_action` | User tapped "Continue" after selecting a country | — |
| `ob_step_country_of_residence_btn_terms_and_conditions` | `user_action` | User tapped the Terms & Conditions link | — |
| `ob_step_country_of_residence_btn_privacy_policy` | `user_action` | User tapped the Privacy Policy link | — |

### Onboarding — Email

| Event Name | Category | Description | Added |
|------------|----------|-------------|-------|
| `ob_step_email_start` | `page_view` | Email input step became visible | — |
| `ob_step_email_btn_continue` | `user_action` | User tapped "Continue" after entering email | — |
| `ob_step_email_btn_skip` | `user_action` | User tapped "Skip" on the email step | — |
| `ob_step_email_validation_otp_start` | `page_view` | Email OTP validation step became visible | — |
| `ob_step_email_validation_otp_btn_continue` | `user_action` | User submitted the email OTP code | — |
| `ob_step_email_validation_otp_btn_resend_code` | `user_action` | User tapped "Resend code" on email OTP step | — |

### Onboarding — Personal Data

| Event Name | Category | Description | Added |
|------------|----------|-------------|-------|
| `ob_step_personal_data_start` | `page_view` | Personal data form step became visible | — |
| `ob_step_personal_data_btn_continue` | `user_action` | User submitted personal data form | — |
| `ob_step_personal_data_btn_skip` | `user_action` | User tapped "Skip" on personal data step | — |

### Onboarding — Legal

| Event Name | Category | Description | Added |
|------------|----------|-------------|-------|
| `ob_step_legal_start` | `page_view` | Legal declarations step became visible | — |
| `ob_step_legal_btn_continue` | `user_action` | User tapped "Continue" on legal step. `eventProperties: { pep }` indicates PEP status | — |
| `ob_step_legal_btn_skip` | `user_action` | User tapped "Skip" on legal step. `eventProperties: { pep }` indicates PEP status | — |

### Onboarding — Identity Validation

| Event Name | Category | Description | Added |
|------------|----------|-------------|-------|
| `ob_step_identity_validation_start` | `page_view` | Identity validation step became visible | — |
| `ob_step_identity_validation_btn_continue` | `user_action` | User tapped "Continue" to start identity validation | — |
| `ob_step_identity_validation_btn_skip` | `user_action` | User tapped "Skip" on identity validation | — |
| `ob_step_identity_validation_tips_impression` | `page_view` | Tips/instructions screen was shown before capture | — |
| `ob_step_identity_validation_tips_btn_continue` | `user_action` | User tapped "Continue" on the tips screen | — |
| `ob_step_identity_validation_camera_permission_error_impression` | `page_error_view` | Camera permission error was displayed | — |
| `ob_step_identity_validation_in_progress_start` | `page_view` | Identity validation processing screen became visible | — |
| `ob_step_identity_validation_error_start` | `page_error_view` | Identity validation error screen became visible. `eventProperties: { title, description }` | — |
| `ob_step_identity_validation_success_start` | `page_view` | Identity validation success screen became visible. `eventProperties: { title, description }` | — |

### Onboarding — Address

| Event Name | Category | Description | Added |
|------------|----------|-------------|-------|
| `ob_step_address_start` | `page_view` | Address input step became visible | — |
| `ob_step_address_btn_continue` | `user_action` | User submitted address form | — |
| `ob_step_address_btn_skip` | `user_action` | User tapped "Skip" on address step | — |
| `ob_step_address_btn_manual_entry` | `user_action` | User chose manual address entry instead of autocomplete | — |
| `ob_step_address_confirmation_start` | `page_view` | Address confirmation step became visible | — |
| `ob_step_address_confirmation_btn_continue` | `user_action` | User confirmed the address | — |
| `ob_step_address_confirmation_btn_skip` | `user_action` | User skipped address confirmation | — |

### Onboarding — Occupation

| Event Name | Category | Description | Added |
|------------|----------|-------------|-------|
| `ob_step_occupation_start` | `page_view` | Occupation selection step became visible | — |
| `ob_step_occupation_btn_continue` | `user_action` | User submitted occupation selection | — |
| `ob_step_occupation_btn_skip` | `user_action` | User tapped "Skip" on occupation step | — |

### Onboarding — Financial Activity

| Event Name | Category | Description | Added |
|------------|----------|-------------|-------|
| `ob_step_financial_activity_start` | `page_view` | Financial activity step became visible | — |
| `ob_step_financial_activity_btn_continue` | `user_action` | User submitted financial activity form | — |
| `ob_step_financial_activity_btn_skip` | `user_action` | User tapped "Skip" on financial activity step | — |

### Onboarding — PIN

| Event Name | Category | Description | Added |
|------------|----------|-------------|-------|
| `ob_step_pin_start` | `page_view` | PIN creation step became visible | — |
| `ob_step_pin_btn_continue` | `user_action` | User submitted the new PIN | — |
| `ob_step_pin_btn_skip` | `user_action` | User tapped "Skip" on PIN creation | — |
| `ob_step_pin_confirmation_start` | `page_view` | PIN confirmation step became visible | — |
| `ob_step_pin_confirmation_btn_continue` | `user_action` | User confirmed the PIN | — |
| `ob_step_pin_confirmation_btn_skip` | `user_action` | User tapped "Skip" on PIN confirmation | — |

### Onboarding — Trusted Device

| Event Name | Category | Description | Added |
|------------|----------|-------------|-------|
| `ob_step_trusted_device_start` | `page_view` | Trusted device registration step became visible | — |
| `ob_step_trusted_device_btn_continue` | `user_action` | User tapped "Continue" to register the device as trusted | — |
| `ob_step_trusted_device_btn_skip` | `user_action` | User tapped "Skip" on trusted device step | — |

### Authentication — Email OTP

| Event Name | Category | Description | Added |
|------------|----------|-------------|-------|
| `auth_email_otp_start` | `page_view` | Email OTP sign-in screen became visible | — |

### Authentication — Phone

| Event Name | Category | Description | Added |
|------------|----------|-------------|-------|
| `auth_phone_input_start` | `page_view` | Phone number input screen became visible | — |
| `auth_phone_input_btn_continue` | `user_action` | User submitted the phone number | — |
| `auth_phone_otp_start` | `page_view` | Phone OTP sign-in screen became visible | — |

### Authentication — Password

| Event Name | Category | Description | Added |
|------------|----------|-------------|-------|
| `auth_password_start` | `page_view` | Password sign-in screen became visible | — |

### Authentication — PIN

| Event Name | Category | Description | Added |
|------------|----------|-------------|-------|
| `auth_pin_start` | `page_view` | PIN sign-in screen became visible | — |
| `auth_pin_btn_submit` | `user_action` | User submitted the PIN for sign-in | — |

### Authentication — Face Match

| Event Name | Category | Description | Added |
|------------|----------|-------------|-------|
| `auth_face_match_start` | `page_view` | Face match intro screen became visible | — |
| `auth_face_match_onfido_start` | `page_view` | Face match Onfido provider screen became visible | — |
| `auth_face_match_incode_start` | `page_view` | Face match Incode provider screen became visible | — |
| `auth_face_match_processing_start` | `page_view` | Face match is processing, waiting screen shown | — |
| `auth_face_match_setup_trusted_device_start` | `page_view` | Trusted device setup screen shown after face match | — |
| `auth_face_match_rejected_start` | `page_view` | Face match was rejected, rejection screen shown | — |
| `auth_face_match_error_start` | `page_error_view` | Face match encountered an error, error screen shown | — |

### Authentication — OTP Actions

| Event Name | Category | Description | Added |
|------------|----------|-------------|-------|
| `submit_otp` | `user_action` | User submitted an OTP code. `eventProperties: { channel }` (email/sms) | — |
| `resend_otp` | `user_action` | User tapped "Resend" OTP code. `eventProperties: { channel }` (email/sms) | — |

### Home

| Event Name | Category | Description | Added |
|------------|----------|-------------|-------|
| `home_start` | `page_view` | Home screen became visible | — |
| `btn_balance_visibility_toggle` | `user_action` | User toggled balance visibility (show/hide) | — |
| `btn_profile` | `user_action` | User tapped the profile button | — |
| `btn_rewards` | `user_action` | User tapped the rewards button | — |
| `btn_add_funds` | `user_action` | User tapped "Add funds" quick action | — |
| `btn_card` | `user_action` | User tapped "Card" quick action | — |
| `btn_send_money` | `user_action` | User tapped "Send money" quick action | — |
| `btn_see_all_transactions` | `user_action` | User tapped "See all" on the transactions list | — |
| `transactions_item_click` | `user_action` | User tapped a specific transaction item | — |
| `home_banner_page_impression` | `page_view` | Full-screen home banner became visible. `eventProperties: { bannerType: "home-page" | "home-header", bannerTitle?: string, bannerDescription?: string, bannerDeepLink?: string }` | 2026-05-11 |
| `home_banner_page_action` | `user_action` | User tapped the primary action button on the full-screen home banner. `eventProperties: { bannerType: "home-page" | "home-header", bannerTitle?: string, bannerDescription?: string, bannerDeepLink?: string }` | 2026-05-11 |
| `home_banner_page_dismiss` | `user_action` | User tapped the dismiss button on the full-screen home banner. `eventProperties: { bannerType: "home-page" | "home-header", bannerTitle?: string, bannerDescription?: string, bannerDeepLink?: string }` | 2026-05-11 |
| `home_banner_header_action` | `user_action` | User tapped a home header banner (whole area or action button). `eventProperties: { bannerType: "home-page" | "home-header", bannerTitle?: string, bannerDescription?: string, bannerDeepLink?: string }` | 2026-05-11 |

### Add Funds — Select Currency

| Event Name | Category | Description | Added |
|------------|----------|-------------|-------|
| `add_funds_select_currency_start` | `page_view` | Currency selection screen became visible | — |
| `btn_select_currency` | `user_action` | User selected a currency | — |

### Add Funds — Select Amount

| Event Name | Category | Description | Added |
|------------|----------|-------------|-------|
| `add_funds_select_amount_start` | `page_view` | Amount selection screen became visible | — |

### Add Funds — Select Method

| Event Name | Category | Description | Added |
|------------|----------|-------------|-------|
| `add_funds_select_method_start` | `page_view` | Payment method selection screen became visible | — |
| `btn_select_method` | `user_action` | User selected a payment method | — |

### Add Funds — Checkout

| Event Name | Category | Description | Added |
|------------|----------|-------------|-------|
| `add_funds_checkout_summary_start` | `page_view` | Checkout summary screen became visible | — |
| `btn_change_amount` | `user_action` | User tapped "Change amount" on checkout | — |
| `btn_change_payment_method` | `user_action` | User tapped "Change payment method" on checkout | — |
| `btn_confirm_pay` | `user_action` | User confirmed the payment on checkout | — |

### PIN Validation

| Event Name | Category | Description | Added |
|------------|----------|-------------|-------|
| `pin_validation_start` | `page_view` | PIN validation screen became visible | — |
| `btn_validate` | `user_action` / `validation_error` | User submitted PIN. On error, `eventProperties: { cause }` with error code | — |
| `pin_validation_btn_forgot_pin` | `user_action` | User tapped "Forgot PIN" | — |

### PIN Recovery — Validation

| Event Name | Category | Description | Added |
|------------|----------|-------------|-------|
| `pin_recovery_validation_start` | `page_navigation` | PIN recovery method routing transition. `eventProperties: { selectedMethod }` | — |

### PIN Recovery — OTP

| Event Name | Category | Description | Added |
|------------|----------|-------------|-------|
| `pin_recovery_otp_start` | `page_view` | PIN recovery OTP screen became visible. `eventProperties: { method }` | — |
| `submit_otp` | `user_action` | User submitted OTP code. `eventProperties: { method }` (fires with `screenName: pin_recovery_validation`) | — |
| `btn_validate` | `validation_error` | OTP validation failed. `eventProperties: { cause }` (fires with `screenName: pin_recovery_validation`) | — |
| `resend_otp` | `user_action` | User tapped "Resend". `eventProperties: { channel }` (fires with `screenName: pin_recovery_validation`) | — |
| `btn_cancel` | `user_action` | User tapped "Cancel" to go back to PIN validation | — |

### PIN Recovery — Resolution

| Event Name | Category | Description | Added |
|------------|----------|-------------|-------|
| `pin_recovery_resolution_start` | `page_navigation` | PIN recovery resolution transition (navigates to next step or PIN creation) | — |

### PIN Recovery — PIN Creation

| Event Name | Category | Description | Added |
|------------|----------|-------------|-------|
| `pin_recovery_creation_start` | `page_view` | New PIN creation screen became visible | — |
| `pin_recovery_confirmation_start` | `page_view` | New PIN confirmation screen became visible | — |
| `submit_pin_creation` | `user_action` | User submitted the new PIN | — |
| `submit_pin_confirmation` | `user_action` | User submitted the PIN confirmation | — |

### PIN Recovery — Face Match

| Event Name | Category | Description | Added |
|------------|----------|-------------|-------|
| `pin_recovery_face_match_start` | `page_view` | Face match intro screen became visible. `eventProperties: { availableMethods }` | — |
| `pin_recovery_face_match_onfido_start` | `page_view` | Face match Onfido provider screen became visible | — |
| `pin_recovery_face_match_incode_start` | `page_view` | Face match Incode provider screen became visible | — |
| `pin_recovery_face_match_processing_start` | `page_view` | Face match processing/waiting screen became visible. `eventProperties: { provider }` | — |
| `pin_recovery_face_match_rejected_start` | `page_view` | Face match rejected screen became visible. `eventProperties: { provider, attempts }` | — |
| `pin_recovery_face_match_error_start` | `page_view` | Face match error screen became visible. `eventProperties: { provider, errorMessage, attempts }` | — |
| `pin_recovery_face_match_setup_trusted_device_start` | `page_view` | Trusted device setup screen became visible after face match | — |

### Biometric Validation

| Event Name | Category | Description | Added |
|------------|----------|-------------|-------|
| `biometric_validation_error_start` | `page_error_view` | Biometric validation error screen became visible | — |
| `biometric_validation_error_btn_continue` | `user_action` | User tapped "Continue" on biometric error screen | — |

### KYC

| Event Name | Category | Description | Added |
|------------|----------|-------------|-------|
| `kyc_validation_start` | `page_view` | KYC validation screen became visible | — |

### Errors & Support

| Event Name | Category | Description | Added |
|------------|----------|-------------|-------|
| `error_dialog_start` | `modal_error_view` | Error dialog was displayed | — |
| `error_page_start` | `page_error_view` | Full-page error screen was displayed | — |
| `contact_support_click` | `user_action` | User tapped "Contact support" on an error screen/dialog | — |

### Generic Buttons

These events are reused across multiple screens and carry meaning from their `screenName` context:

| Event Name | Category | Description | Added |
|------------|----------|-------------|-------|
| `btn_continue` | `user_action` | User tapped a "Continue" button | — |
| `btn_submit` | `user_action` | User tapped a "Submit" button | — |
| `btn_ok` | `user_action` | User tapped an "OK" button | — |
| `btn_cancel` | `user_action` | User tapped a "Cancel" button | — |
| `btn_retry` | `user_action` | User tapped a "Retry" button | — |
| `btn_back` | `user_action` | User tapped "Back" to return to previous screen | — |
