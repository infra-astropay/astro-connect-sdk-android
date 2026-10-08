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
| `sessionId` | `string` | Unique ID generated per session |
| `appVersion` | `string` | Version of the SDK content that emitted the event. It is not the version of the native SDK you installed — see the note under [KYC](#kyc) for the one exception |
| `platform` | `'ios' \| 'android'` | Current platform |

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
| `impression` | An element or step of a self-service flow became visible, or reported a state change (used by the [Dynamic Self-Service Flows](#dynamic-self-service-flows-dssf) events) |

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
| `convert` | Currency conversion flow screen |

### Pix

| Screen Name | Description |
|-------------|-------------|
| `pix_home` | Pix home screen (send/receive money entry point) |
| `pix_sm_transfer_home` | Pix send money — transfer method selection screen (by phone number or Pix key) |
| `pix_sm_transfer` | Pix transfer screen — enter recipient (phone number, Pix key, or recent contact) |
| `pix_sm_confirm` | Pix transfer confirmation screen — review and confirm before sending |
| `pix_sm_status_success` | Pix transfer status screen — success state (transfer completed) |
| `pix_random_key_home` | Assign a random Pix key screen |
| `pix_sm_amount` | Pix transfer amount-entry screen |

### Send Money

| Screen Name | Description |
|-------------|-------------|
| `sm_rails_list` | Send money — rail/method selection screen |

### Activities

| Screen Name | Description |
|-------------|-------------|
| `activities` | Activities/transactions list screen (generic, any or no filter) |
| `pix_activities` | Activities/transactions list screen, filtered to Pix transfers |

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

### My Account

| Screen Name | Description |
|-------------|-------------|
| `my_account` | My Account home screen |
| `complete_profile` | Complete profile screen |
| `personal_data` | Personal data screen |
| `security` | Security settings screen |
| `my_account_face_match_intro` | Face match introduction/start screen (my-account pending task) |
| `my_account_face_match_onfido` | Face match via Onfido provider (my-account) |
| `my_account_face_match_incode` | Face match via Incode provider (my-account) |
| `my_account_face_match_processing` | Face match processing/waiting screen (my-account) |
| `my_account_face_match_rejected` | Face match was rejected (my-account) |
| `my_account_face_match_error` | Face match encountered an error (my-account) |

### Wallet

| Screen Name | Description |
|-------------|-------------|
| `wallet_add_currency` | Add a new wallet currency panel |
| `wallet_currency_home` | Wallet detail/currency home panel (per-currency balance and quick actions) |

### CCI Account

| Screen Name | Description |
|-------------|-------------|
| `cci_account_check` | Screen that tracks the creation of the user's CCI account, opened by the `check_cci` banner destination |
| `cci_account_onboarding` | Introduction to the CCI account, where the user starts the request |
| `cci_account_request` | Form where the user fills in the details needed to request the CCI account, one step at a time |
| `cci_account_request_success` | Confirmation that the CCI account request was sent |
| `cci_account_status_pending` | The CCI account request is being processed |
| `cci_account_status_failed` | The CCI account could not be created, is blocked, or is not available for the user |
| `cci_account_info` | The details of the user's CCI account, ready to copy or share |

### Errors

| Screen Name | Description |
|-------------|-------------|
| `page_error` | Full-page error screen |
| `modal_error` | Error dialog/modal |

### Support

| Screen Name | Description |
|-------------|-------------|
| `chat` | In-app support chat |

### Dynamic Self-Service Flows

| Screen Name | Description |
|-------------|-------------|
| `dssf_workflow` | Coordinator-level (no specific step active) |
| `dssf_workflow_list` | Workflow selection list screen |
| `dssf_form` | FORM step screen |
| `dssf_context` | CONTEXT step screen |
| `dssf_waiting_for_update` | WAITING_FOR_UPDATE step screen |
| `dssf_operation_result` | OPERATION_RESULT step screen |
| `dssf_upload_file` | UPLOAD_FILE step screen |

### AstroPay Card

| Screen Name | Description |
|-------------|-------------|
| `card_retry_order` | Card order status/retry screen |
| `card_more_actions` | "More actions" modal on the AstroPay Card home screen |
| `card_home` | AstroPay Card home screen |
| `card_request_init` | AstroPay Card request flow — initial/intro screen |

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
| `ob_step_identity_validation_tips_impression` | `modal_view` | Tips/instructions dialog was shown before capture | — |
| `ob_step_identity_validation_tips_btn_continue` | `user_action` | User tapped "Continue" on the tips screen | — |
| `ob_step_identity_validation_camera_permission_error_impression` | `modal_view` | Camera permission error dialog was displayed | — |
| `ob_step_identity_validation_in_progress_start` | `page_view` | Identity validation processing screen became visible | — |
| `ob_step_identity_validation_in_progress_btn_close_and_continue` | `user_action` | User tapped the button that leaves the SDK while identity validation is still in progress. The button is shown only on some integrations; the SDK closes right after with code `CLOSED_BY_USER_NAVIGATED_BACK` | 2026-07-23 |
| `ob_step_identity_validation_error_start` | `page_view` | Identity validation error screen became visible. `eventProperties: { title, description }` | — |
| `ob_step_identity_validation_success_start` | `page_view` | Identity validation success screen became visible. `eventProperties: { title, description }` | — |
| `ob_step_identity_validation_banner_impression` | `page_view` | A banner on the identity validation step became visible. `eventProperties: { bannerType: "ob-kyc-start", bannerTitle?: string, bannerDescription?: string, bannerDeepLink?: string }` | 2026-09-11 |
| `ob_step_identity_validation_banner_action` | `user_action` | User tapped a banner on the identity validation step (whole area or action button). `eventProperties: { bannerType: "ob-kyc-start", bannerTitle?: string, bannerDescription?: string, bannerDeepLink?: string }` | 2026-09-11 |
| `ob_step_identity_validation_banner_dismiss` | `user_action` | User tapped the dismiss (X) button on a banner on the identity validation step. `eventProperties: { bannerType: "ob-kyc-start", bannerTitle?: string, bannerDescription?: string, bannerDeepLink?: string }` | 2026-09-11 |

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
| `home_banner_page_impression` | `page_view` | Full-screen home banner became visible. `eventProperties: { bannerType: "home-page", bannerTitle?: string, bannerDescription?: string, bannerDeepLink?: string }` | 2026-05-11 |
| `home_banner_page_action` | `user_action` | User tapped the primary action button on the full-screen home banner. `eventProperties: { bannerType: "home-page", bannerTitle?: string, bannerDescription?: string, bannerDeepLink?: string }` | 2026-05-11 |
| `home_banner_page_dismiss` | `user_action` | User tapped the dismiss button on the full-screen home banner. `eventProperties: { bannerType: "home-page", bannerTitle?: string, bannerDescription?: string, bannerDeepLink?: string }` | 2026-05-11 |
| `home_banner_page_auto_redirect` | `page_navigation` | A full-screen home banner sent with `bannerAutoRedirect: true` took the user straight to its destination without being shown. Emitted once per redirect; no `home_banner_page_impression` or `home_banner_page_action` is sent for it. `eventProperties: { bannerType: "home-page", bannerTitle?: string, bannerDescription?: string, bannerDeepLink?: string }` | 2026-09-25 |
| `home_banner_header_action` | `user_action` | User tapped a home header banner (whole area or action button). `eventProperties: { bannerType: "home-header", bannerTitle?: string, bannerDescription?: string, bannerDeepLink?: string }` | 2026-05-11 |
| `home_banner_header_dismiss` | `user_action` | User tapped the dismiss (X) button on a home header banner (the dismiss control appears when the banner sets `bannerDismissible: true`, or omits it and has `bannerDeepLink: "none"`). `eventProperties: { bannerType: "home-header", bannerTitle?: string, bannerDescription?: string, bannerDeepLink?: string }` | 2026-07-13 |
| `btn_wallet_add_currency` | `user_action` | User tapped the "add currency" button on the Home screen's balance widget, to add a new wallet currency | 2026-09-16 |

> Home header banners report action and dismiss only — there is no impression event for them. Banners on the add-funds surfaces do report impressions (see below).
>
> Quick actions are reported as `btn_<action id>` in lowercase. `btn_add_funds`, `btn_card` and `btn_send_money` above are the common ones; any other quick action enabled for your integration follows the same pattern.

### Add Funds — Select Currency

| Event Name | Category | Description | Added |
|------------|----------|-------------|-------|
| `add_funds_select_currency_start` | `page_view` | Currency selection screen became visible | — |
| `btn_select_currency` | `user_action` | User selected a currency | — |
| `add_funds_select_currency_banner_impression` | `page_view` | A banner on the currency selection screen became visible. `eventProperties: { bannerType: "topup-select-currency", bannerTitle?: string, bannerDescription?: string, bannerDeepLink?: string }` | 2026-08-04 |
| `add_funds_select_currency_banner_action` | `user_action` | User tapped a banner on the currency selection screen (whole area or action button). `eventProperties: { bannerType: "topup-select-currency", bannerTitle?: string, bannerDescription?: string, bannerDeepLink?: string }` | 2026-08-04 |
| `add_funds_select_currency_banner_dismiss` | `user_action` | User tapped the dismiss (X) button on a banner on the currency selection screen. `eventProperties: { bannerType: "topup-select-currency", bannerTitle?: string, bannerDescription?: string, bannerDeepLink?: string }` | 2026-08-04 |

> The currency selection screen is skipped for users who hold a single wallet, so these events only occur for users with more than one currency.

### Add Funds — Select Amount

| Event Name | Category | Description | Added |
|------------|----------|-------------|-------|
| `add_funds_select_amount_start` | `page_view` | Amount selection screen became visible | — |

### Add Funds — Select Method

| Event Name | Category | Description | Added |
|------------|----------|-------------|-------|
| `add_funds_select_method_start` | `page_view` | Payment method selection screen became visible | — |
| `btn_select_method` | `user_action` | User selected a payment method. `eventProperties: { methodName }` | — |
| `add_funds_select_method_banner_impression` | `page_view` | A banner on the payment method selection screen became visible. `eventProperties: { bannerType: "topup-select-method", bannerTitle?: string, bannerDescription?: string, bannerDeepLink?: string }` | 2026-08-04 |
| `add_funds_select_method_banner_action` | `user_action` | User tapped a banner on the payment method selection screen (whole area or action button). `eventProperties: { bannerType: "topup-select-method", bannerTitle?: string, bannerDescription?: string, bannerDeepLink?: string }` | 2026-08-04 |
| `add_funds_select_method_banner_dismiss` | `user_action` | User tapped the dismiss (X) button on a banner on the payment method selection screen. `eventProperties: { bannerType: "topup-select-method", bannerTitle?: string, bannerDescription?: string, bannerDeepLink?: string }` | 2026-08-04 |

### Add Funds — Checkout

| Event Name | Category | Description | Added |
|------------|----------|-------------|-------|
| `add_funds_checkout_summary_start` | `page_view` | Checkout summary screen became visible | — |
| `btn_change_amount` | `user_action` | User tapped "Change amount" on checkout | — |
| `btn_change_payment_method` | `user_action` | User tapped "Change payment method" on checkout | — |
| `btn_confirm_pay` | `user_action` | User confirmed the payment on checkout | — |

### Wallet — Convert

| Event Name | Category | Description | Added |
|------------|----------|-------------|-------|
| `btn_convert_money` | `user_action` | User tapped the "Convert money" commit button on the Convert summary screen. | 2026-09-14 |
| `btn_convert_done` | `user_action` | User tapped the "Done" button on the Convert completed/success screen, closing the flow and returning to the wallet home. | 2026-09-14 |

### Pix

| Event Name | Category | Description | Added |
|------------|----------|-------------|-------|
| `btn_receive_copy_paste` | `user_action` | User tapped the "Copy and paste" option under Receive money on the Pix home screen | 2026-09-14 |
| `btn_receive_qr_code` | `user_action` | User tapped the "QR code" option under Receive money on the Pix home screen | 2026-09-14 |
| `btn_send_transfer` | `user_action` | User tapped the "PIX transfer" option under Send money on the Pix home screen | 2026-09-14 |
| `btn_send_schedule_transfer` | `user_action` | User tapped the "Schedule transfer" option under Send money on the Pix home screen | 2026-09-14 |
| `btn_send_copy_paste` | `user_action` | User tapped the "Copy and paste" option under Send money on the Pix home screen | 2026-09-14 |
| `btn_send_bank_transfer` | `user_action` | User tapped the "Bank account transfer" option under Send money on the Pix home screen | 2026-09-14 |
| `btn_send_qr_code` | `user_action` | User tapped the "Scan QR code" option under Send money on the Pix home screen | 2026-09-14 |
| `btn_pix_my_keys` | `user_action` | User tapped the "My Pix keys" menu item on the Pix home screen | 2026-09-14 |
| `btn_pix_transactions` | `user_action` | User tapped the "Transactions" menu item on the Pix home screen | 2026-09-14 |
| `btn_pix_settings` | `user_action` | User tapped the "My settings" menu item on the Pix home screen | 2026-09-14 |
| `btn_pix_reply_claim` | `user_action` | User tapped the "Reply to a Pix claim" menu item on the Pix home screen | 2026-09-14 |
| `btn_pix_complain` | `user_action` | User tapped the "Complain here" menu item on the Pix home screen | 2026-09-14 |
| `btn_pix_transfer_phone_number` | `user_action` | User selected "Transfer by phone number" on the Pix transfer method selection screen | 2026-09-14 |
| `btn_pix_transfer_key` | `user_action` | User selected "Transfer by Pix key" on the Pix transfer method selection screen | 2026-09-14 |
| `pix_sm_transfer_start` | `page_view` | Pix transfer screen became visible | 2026-09-14 |
| `btn_pix_confirm_transfer` | `user_action` | User tapped the confirm button on the Pix transfer confirmation screen | 2026-09-14 |
| `pix_sm_status_success_start` | `page_view` | Pix transfer status screen showed the success state (transfer completed) | 2026-09-14 |
| `btn_confirm_random_key` | `user_action` | User tapped the confirm button to assign a random Pix key | 2026-09-15 |
| `pix_sm_amount_start` | `page_view` | Pix transfer amount-entry screen became visible | 2026-09-15 |

### Send Money

| Event Name | Category | Description | Added |
|------------|----------|-------------|-------|
| `btn_rail_cvu` | `user_action` | User selected the CVU transfer rail on the Send money rail selection screen | 2026-09-14 |
| `btn_rail_iban_eur` | `user_action` | User selected the IBAN/EUR transfer rail on the Send money rail selection screen | 2026-09-14 |
| `btn_rail_astropay` | `user_action` | User selected the AstroPay-to-AstroPay transfer rail on the Send money rail selection screen | 2026-09-14 |
| `btn_rail_pix` | `user_action` | User selected the PIX transfer rail on the Send money rail selection screen | 2026-09-14 |
| `btn_rail_cci` | `user_action` | User selected the CCI transfer rail on the Send money rail selection screen | 2026-09-14 |

### Activities

| Event Name | Category | Description | Added |
|------------|----------|-------------|-------|
| `activities_start` | `page_view` | Activities list screen became visible (generic view) | 2026-09-15 |
| `pix_activities_start` | `page_view` | Activities list screen became visible, filtered to Pix transfers | 2026-09-15 |

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

### My Account

| Event Name | Category | Description | Added |
|------------|----------|-------------|-------|
| `btn_my_account_complete_profile` | `user_action` | User tapped the "Complete profile" button on the My Account home screen | 2026-09-16 |
| `btn_my_account_chat` | `user_action` | User tapped the chat/support entry in the My Account menu | 2026-09-16 |
| `btn_my_account_link` | `user_action` | User tapped an item in the My Account dynamic link list | 2026-09-16 |
| `complete_profile_start` | `page_view` | Complete profile screen became visible | 2026-09-16 |
| `personal_data_start` | `page_view` | Personal data screen became visible | 2026-09-16 |
| `security_start` | `page_view` | Security settings screen became visible | 2026-09-16 |

### My Account — Face Match

| Event Name | Category | Description | Added |
|------------|----------|-------------|-------|
| `my_account_face_match_start` | `page_view` | Face match intro screen became visible (my-account pending task) | 2026-05-27 |
| `my_account_face_match_onfido_start` | `page_view` | Face match Onfido provider screen became visible | 2026-05-27 |
| `my_account_face_match_incode_start` | `page_view` | Face match Incode provider screen became visible | 2026-05-27 |
| `my_account_face_match_processing_start` | `page_view` | Face match processing/waiting screen became visible. `eventProperties: { provider }` | 2026-05-27 |
| `my_account_face_match_rejected_start` | `page_view` | Face match rejected screen became visible. `eventProperties: { provider, attempts }` | 2026-05-27 |
| `my_account_face_match_error_start` | `page_view` | Face match error screen became visible. `eventProperties: { provider, errorMessage, attempts }` | 2026-05-27 |

### Biometric Validation

| Event Name | Category | Description | Added |
|------------|----------|-------------|-------|
| `biometric_validation_error_start` | `page_view` | Biometric validation error screen became visible | — |
| `biometric_validation_error_btn_continue` | `user_action` | User tapped "Continue" on biometric error screen | — |

### KYC

| Event Name | Category | Description | Added |
|------------|----------|-------------|-------|
| `kyc_validation_start` | `page_view` | KYC validation screen became visible | — |
| `kyc_caf_native_start` | `page_view` | KYC flow started | 2026-06-17 |
| `kyc_caf_native_result` | `user_action` | KYC flow completed with a result. May also carry the optional properties listed under the table | 2026-06-17 |
| `kyc_caf_native_loading` | `page_view` | Identity verification began initializing (fires once at the start of the KYC flow) | 2026-07-02 |
| `kyc_caf_native_document_captured` | `page_view` | KYC flow produced a document result. `eventProperties: { module: "documentDetector" }`, plus the optional properties listed under the table | 2026-07-02 |
| `kyc_caf_native_liveness_captured` | `page_view` | KYC flow produced a liveness result. `eventProperties: { module: "faceLiveness" }` | 2026-07-02 |
| `kyc_caf_native_document_type_selected` | `user_action` | User chose which document to present. `eventProperties: { module: "documentDetector", selectedDocumentType }` | 2026-09-10 |
| `kyc_caf_native_capture_method_selected` | `user_action` | User chose how to provide the document. `eventProperties: { module: "documentDetector", captureMode }`, plus `selectedDocumentType` when known | 2026-09-10 |
| `kyc_caf_native_side_capture_started` | `page_view` | Capture began for one side of the document. `eventProperties: { module: "documentDetector", side, step, stepCount }`, plus the optional properties below | 2026-09-10 |
| `kyc_caf_native_side_captured` | `page_view` | One side of the document was captured. `eventProperties: { module: "documentDetector", side, capturedSides, stepCount }`, plus the optional properties below | 2026-09-10 |
| `kyc_caf_native_liveness_start` | `page_view` | Liveness verification began. `eventProperties: { module: "faceLiveness" }` | 2026-09-10 |
| `kyc_caf_native_liveness_error` | `page_view` | Liveness reported a problem the user can retry past — **not** a terminal outcome. `eventProperties: { module: "faceLiveness", errorDescription }` | 2026-09-10 |
| `kyc_caf_native_capture_cancelled` | `user_action` | User closed a document capture and stayed in the flow — **not** a terminal outcome. `eventProperties: { module: "documentDetector", capturedSides, totalSides, backTo }`, plus the optional properties below. `capturedSides` and `totalSides` are strings; `backTo` is the screen the user returned to: `picker` (document list), `mode` (capture method list) or `document_gate` (the screen between two sides) | 2026-10-07 |
| `kyc_caf_native_back` | `user_action` | User went back one screen inside the KYC flow — **not** a terminal outcome. `eventProperties: { module, from, to }`, plus the optional properties below. `from` and `to` name the screens: `picker`, `mode`, `capture_tips`, `document_gate`, `liveness_tips` or `leave_warning` (the confirmation shown before leaving the flow) | 2026-10-07 |

> **Capture steps vs documents.** `kyc_caf_native_side_capture_started` and `kyc_caf_native_side_captured` describe capture steps. A step may capture one physical side or both sides together (`side: single`). `kyc_caf_native_document_captured` fires once after the complete document has been captured. A retried step can produce another start event.
>
> `side` is `front`, `back` or `single` (a document captured whole in one go). `step`, `stepCount` and `capturedSides` are all sent **as strings**, not numbers.
>
> **`kyc_caf_native_liveness_error` is not terminal.** It reports feedback from the liveness provider — the user is still in the flow and can retry. The only event that ends a KYC attempt is `kyc_caf_native_result`, and it is emitted once per completed attempt. Each attempt produces exactly one `kyc_caf_native_start` and one `kyc_caf_native_result`, including when verification switches to the fallback engine before capture; in that case `kyc_caf_native_loading` can appear twice in the attempt.

> **Note:** `kyc_caf_native_loading`, `kyc_caf_native_document_captured`, and `kyc_caf_native_liveness_captured` can come from either of the two identity-verification engines the SDK uses, depending on which one handles the flow. The event names, their categories and `screenName: kyc_validation` are identical in both cases, so anything keyed on the event name works across both. Their base fields differ: with the native KYC engine, `sessionId` is empty and `appVersion` reflects the native SDK version; otherwise they carry the same `sessionId` and `appVersion` as every other event in this document. On `kyc_caf_native_document_captured`, the optional properties listed below are only ever present in the second case.

**Optional `eventProperties` on the CAF KYC events.** The properties below can accompany `kyc_caf_native_document_captured` and `kyc_caf_native_result`. **Every one of them is optional.** Each is included only when it has a value — it is never sent as an empty string and never as `null`. A property that is not there is normal and expected: it is not an error, and it is not a missing field. Read them defensively (check the key is present before using it) and never require one to arrive.

| Property | Events | Value | Absent when |
|----------|--------|-------|-------------|
| `selectedDocumentType` | `kyc_caf_native_document_captured`, `kyc_caf_native_result`, `kyc_caf_native_side_capture_started`, `kyc_caf_native_side_captured`, `kyc_caf_native_capture_cancelled`, `kyc_caf_native_back` | The document type the user selected | No document type was selected |
| `captureMode` | `kyc_caf_native_document_captured`, `kyc_caf_native_result`, `kyc_caf_native_side_capture_started`, `kyc_caf_native_side_captured`, `kyc_caf_native_capture_cancelled`, `kyc_caf_native_back` | How the document was provided: `automatic` (captured with the camera) or `upload` (a file supplied by the user) | No capture method was chosen |
| `capturedSides` | `kyc_caf_native_document_captured` | How many capture steps have completed, **as a string** (for example `"1"`). One step may cover both physical sides | The flow does not report a side count |
| `documentType` | `kyc_caf_native_document_captured` | The document type detected by the identity provider. It can differ from `selectedDocumentType`, which is the one the user chose — they are separate properties on purpose | The provider's result cannot be read |

> **Capture methods vary by host version.** Android versions with file selection support can report `upload`; older versions offer only the camera (`automatic`). Both methods are available on iOS. Absence of upload events on an older Android host is expected.

### Wallet

| Event Name | Category | Description | Added |
|------------|----------|-------------|-------|
| `btn_wallet_add_currency_close` | `user_action` | User closed the add-currency panel (X icon) | 2026-09-16 |
| `btn_wallet_add_currency_confirm` | `user_action` | User confirmed adding the selected currency wallet | 2026-09-16 |
| `btn_wallet_quick_add_funds` | `user_action` | User tapped the "Add funds" quick action on the wallet detail panel. `eventProperties: { currency }` | 2026-09-16 |
| `btn_wallet_quick_send_money` | `user_action` | User tapped the "Send" quick action on the wallet detail panel. `eventProperties: { currency }` | 2026-09-16 |
| `btn_wallet_quick_receive_money` | `user_action` | User tapped the "Receive" quick action on the wallet detail panel. `eventProperties: { currency }` | 2026-09-16 |
| `btn_wallet_quick_exchange` | `user_action` | User tapped the "Exchange" quick action on the wallet detail panel. `eventProperties: { currency }` | 2026-09-16 |
| `btn_wallet_account_details` | `user_action` | User tapped the account details button on the wallet detail panel, opening the account details view (CVU/IBAN/CLABE/USD/CCI) | 2026-09-16 |

### CCI Account — Check

| Event Name | Category | Description | Added |
|------------|----------|-------------|-------|
| `cci_account_check_pending` | `page_view` | The account is being created and the progress screen is shown with a loader. Sent once when the screen enters this state, not on every automatic refresh. `eventProperties: { status?: string }` | 2026-09-25 |
| `cci_account_check_pending_slow` | `page_view` | The account has been in creation for over a minute; the progress screen now says it is taking longer than expected and lets the user leave and come back later. Sent once per wait. `eventProperties: { status?: string }` | 2026-09-29 |
| `cci_account_check_wait_prompt` | `modal_view` | After about two minutes of waiting, automatic refreshes pause and a dialog asks whether to keep waiting or come back later. `eventProperties: { status?: string }` | 2026-09-29 |
| `cci_account_check_success` | `page_view` | The account was created and the confirmation screen is shown. `eventProperties: { status?: string }` | 2026-09-25 |
| `cci_account_check_rejected` | `page_error_view` | The account creation was rejected; the user can leave the SDK. `eventProperties: { status?: string }` | 2026-09-29 |
| `cci_account_check_error` | `page_error_view` | The account status could not be retrieved, or was not recognized; the user can try again. `status` is absent when the request itself failed. `eventProperties: { status?: string }` | 2026-09-25 |
| `cci_account_check_email_required` | `page_view` | The user has to set or confirm their email before going on; the email-required step is shown instead of the progress, confirmation or rejection screen, whatever the creation status is. `eventProperties: { status?: string }` | 2026-10-02 |
| `btn_cci_account_check_continue` | `user_action` | User tapped "Continue" on the confirmation screen. `eventProperties: { status?: string }` | 2026-09-25 |
| `btn_cci_account_check_verify_email` | `user_action` | User tapped "Continue" on the email-required step and is taken to the email verification screen; they return to this screen afterwards and the status is checked again. `eventProperties: { status?: string }` | 2026-10-02 |
| `btn_cci_account_check_retry` | `user_action` | User tapped "Try again" on the error screen to check the status again. `eventProperties: { status?: string }` | 2026-09-25 |
| `btn_cci_account_check_keep_waiting` | `user_action` | User chose to keep waiting in the dialog; the status is checked again right away and automatic refreshes resume. `eventProperties: { status?: string }` | 2026-09-29 |
| `btn_cci_account_check_close` | `user_action` | User tapped "Come back later" on the progress or error screen, on the email-required step or in the keep-waiting dialog, or "OK" on the rejection screen; the SDK closes right after with code `CLOSED_BY_USER_NAVIGATED_BACK` from the progress screen, the email-required step or the dialog, or `CLOSED_BY_USER_DISMISSED_ERROR` from the others. `eventProperties: { status?: string }` | 2026-09-25 |

> On the `cci_account_check` screen, `status` is the creation status returned by AstroPay: `PENDING`, `SUCCESS` or `REJECTED`. Treat unknown values as opaque. On the email-required step, `status` is still the creation status and can be any of these values, including `SUCCESS`.

### CCI Account — Request

| Event Name | Category | Description | Added |
|------------|----------|-------------|-------|
| `cci_account_onboarding_start` | `page_view` | The CCI account introduction became visible. | 2026-09-25 |
| `btn_cci_account_onboarding_start` | `user_action` | User tapped the button that starts the CCI account request. | 2026-09-25 |
| `cci_account_request_step_start` | `page_view` | A step of the request form became visible. `eventProperties: { stepIdentifier: string }` | 2026-09-25 |
| `btn_cci_account_request_step_continue` | `user_action` | User completed a step of the request form and moved to the next one. `eventProperties: { stepIdentifier: string }` | 2026-09-25 |
| `btn_cci_account_request_submit` | `user_action` | User sent the request from the last step of the form. Not emitted when the request needs no details and is sent without the user filling in the form. | 2026-09-25 |
| `cci_account_request_success_start` | `page_view` | The request was sent and its confirmation became visible. | 2026-09-25 |
| `btn_cci_account_request_success_ok` | `user_action` | User tapped the button on the confirmation. | 2026-09-25 |

### CCI Account — Status and Details

| Event Name | Category | Description | Added |
|------------|----------|-------------|-------|
| `cci_account_status_pending_start` | `page_view` | The screen showing that the request is being processed became visible. `status` is `CREATION_PENDING`, or `KYC_PENDING` while the user's identity is still being validated. `eventProperties: { status: string }` | 2026-09-25 |
| `btn_cci_account_status_pending_continue` | `user_action` | User tapped "Continue" on the processing screen and returned to the home screen. `eventProperties: { status: string }` | 2026-09-25 |
| `cci_account_status_failed_start` | `page_error_view` | The screen explaining why there is no CCI account became visible. `status` is `CREATION_FAILED`, `BLOCKED` or `NOT_ALLOWED`. `eventProperties: { status: string }` | 2026-09-25 |
| `btn_cci_account_status_failed_retry` | `user_action` | User tapped the button to request the account again (only offered for `CREATION_FAILED`). `eventProperties: { status: string }` | 2026-09-25 |
| `btn_cci_account_status_failed_support` | `user_action` | User tapped the button to contact support. `eventProperties: { status: string }` | 2026-09-25 |
| `btn_cci_account_status_failed_close` | `user_action` | User tapped "Close" and returned to the home screen. `eventProperties: { status: string }` | 2026-09-25 |
| `cci_account_info_start` | `page_view` | The CCI account details screen became visible. | 2026-09-25 |
| `btn_cci_account_info_copy` | `user_action` | User copied one of the account details. `eventProperties: { field: string }` — `field` identifies the detail as defined by AstroPay; treat it as opaque | 2026-09-25 |
| `btn_cci_account_info_share` | `user_action` | User tapped the button that copies all the account details at once. | 2026-09-25 |

> When the account details cannot be loaded, or sending the request fails, the SDK shows its standard error screen or dialog and reports it with `error_page_start` or `error_dialog_start` — see [Errors & Support](#errors--support). No CCI-specific event is sent for those.

> `stepIdentifier` names the form step as defined by AstroPay; treat it as opaque.

> `status` is the account status returned by AstroPay for the user's CCI account (for example `CREATION_PENDING`, `ACTIVE` or `CREATION_FAILED`). Treat unknown values as opaque.

### Errors & Support

| Event Name | Category | Description | Added |
|------------|----------|-------------|-------|
| `error_dialog_start` | `modal_error_view` | Error dialog was displayed | — |
| `error_page_start` | `page_error_view` | Full-page error screen was displayed | — |
| `contact_support_click` | `user_action` | User tapped "Contact support" on an error screen/dialog | — |
| `chat_open` | `page_view` | In-app support chat became visible. `eventProperties: { sectionApp: string }` — `sectionApp` is the app section the chat was opened from (e.g. `HELP_CENTER`, `TRANSFER`, `RECHARGE`) | 2026-09-16 |
| `chat_close` | `user_action` | User dismissed the in-app support chat | 2026-09-16 |

> `chat_open` and `chat_close` carry `screenName: chat`. `chat_open` fires only once the support chat is actually shown — tapping a help button when the chat is unavailable does not emit it. `chat_close` is not emitted when the chat closes because the user signs out.

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

### Dynamic Self-Service Flows (DSSF)

| Event Name | Category | Description | Added |
|------------|----------|-------------|-------|
| `dssf_workflow_list_start` | `impression` | Workflow list screen became visible | 2026-05-29 |
| `dssf_workflow_list_btn_select` | `user_action` | User selected a workflow from the list. `eventProperties: { workflow_type }` | 2026-05-29 |
| `dssf_workflow_start` | `impression` | A specific workflow started running. `eventProperties: { workflowType }` | 2026-05-29 |
| `dssf_step_form_start` | `impression` | FORM step became visible. `eventProperties: { workflowType, workflowStepName? }` | 2026-05-29 |
| `dssf_step_form_btn_continue` | `user_action` | User submitted a valid FORM step. `eventProperties: { workflowType, workflowStepName? }` | 2026-05-29 |
| `dssf_step_form_validation_error` | `user_action` | User attempted to submit an invalid FORM. `eventProperties: { workflowType, workflowStepName? }` | 2026-05-29 |
| `dssf_step_context_start` | `impression` | CONTEXT step became visible. `eventProperties: { workflowType, workflowStepName? }` | 2026-05-29 |
| `dssf_step_context_btn_continue` | `user_action` | User tapped Continue on a CONTEXT step. `eventProperties: { workflowType, workflowStepName? }` | 2026-05-29 |
| `dssf_step_waiting_for_update_start` | `impression` | WAITING_FOR_UPDATE step became visible. `eventProperties: { workflowType, workflowStepName? }` | 2026-05-29 |
| `dssf_step_waiting_for_update_btn_cancel` | `user_action` | User cancelled while waiting for update. `eventProperties: { workflowType, workflowStepName? }` | 2026-05-29 |
| `dssf_step_operation_result_start` | `impression` | OPERATION_RESULT step became visible. `eventProperties: { workflowType, workflowStepName? }` | 2026-05-29 |
| `dssf_step_operation_result_btn_done` | `user_action` | User tapped Done on a SUCCESS result. `eventProperties: { workflowType, workflowStepName? }` | 2026-05-29 |
| `dssf_step_operation_result_btn_close` | `user_action` | User tapped Close on a FAILURE result. `eventProperties: { workflowType, workflowStepName? }` | 2026-05-29 |
| `dssf_step_operation_result_btn_help` | `user_action` | User tapped Help/Contact support. `eventProperties: { workflowType, workflowStepName? }` | 2026-05-29 |
| `dssf_step_upload_file_start` | `impression` | UPLOAD_FILE step became visible. `eventProperties: { workflowType, workflowStepName? }` | 2026-05-29 |
| `dssf_step_upload_file_upload_success` | `impression` | File uploaded successfully. `eventProperties: { workflowType, workflowStepName? }` | 2026-05-29 |
| `dssf_step_upload_file_upload_error` | `impression` | File upload failed. `eventProperties: { workflowType, workflowStepName? }` | 2026-05-29 |
| `dssf_step_upload_file_btn_continue` | `user_action` | User tapped Continue after successful upload. `eventProperties: { workflowType, workflowStepName? }` | 2026-05-29 |
| `dssf_workflow_completed` | `impression` | Workflow reached completion. `eventProperties: { workflowType, terminalAction?: "LOGOUT" }` — `terminalAction` is present only when completing the workflow ends the user's session (see note below) | 2026-05-29 |
| `dssf_workflow_closed` | `impression` | Workflow was closed before completion. `eventProperties: { workflowType }` | 2026-05-29 |
| `dssf_workflow_error` | `impression` | Workflow encountered an unrecoverable error. `eventProperties: { workflowType }` | 2026-05-29 |

> **Note (2026-09-14):** DSSF events now include workflow context in `eventProperties`. This is an additive change: no events were added or removed, and existing event names, categories, and properties are unchanged.
>
> - **`workflowType`** (`string`) — the workflow the user is in (e.g. `"ACCOUNT_CLOSURE"`). Present on `dssf_workflow_start`, every `dssf_step_*` event, `dssf_workflow_completed`, `dssf_workflow_closed`, and `dssf_workflow_error`. It is not present on `dssf_workflow_list_start`. `dssf_workflow_list_btn_select` still reports the selected workflow through its existing `workflow_type` property.
> - **`workflowStepName`** (`string`, optional) — the name of the current step (e.g. `"CONFIRMATION"`). Present on `dssf_step_*` events only when the step has a name.
> - **`terminalAction`** (`"LOGOUT"`, optional) — present on `dssf_workflow_completed` when completing the workflow ends the user's session (for example, account closure). This event is sent right before the SDK closes with `CLOSED_BY_USER_SIGNED_OUT`.
>
> Step names are defined by AstroPay and may change. Base your integration on `workflowType` and the event name, and treat `workflowStepName` as informational only.

**Example — account closure (`workflowType: "ACCOUNT_CLOSURE"`).** A typical sequence when the user closes their account (every event below also carries `workflowType: "ACCOUNT_CLOSURE"`):

| # | Event Name | Additional `eventProperties` |
|---|------------|------------------------------|
| 1 | `dssf_workflow_start` | — |
| 2 | `dssf_step_context_start` | `workflowStepName: "INTRO"` |
| 3 | `dssf_step_context_btn_continue` | `workflowStepName: "INTRO"` |
| … | *(further steps)* | |
| 4 | `dssf_step_form_start` | `workflowStepName: "REASON_SELECTION"` |
| 5 | `dssf_step_form_btn_continue` | `workflowStepName: "REASON_SELECTION"` |
| 6 | `dssf_step_context_start` | `workflowStepName: "CONFIRMATION"` |
| 7 | `dssf_step_operation_result_start` | `workflowStepName: "SUCCESS"` |
| 8 | `dssf_step_operation_result_btn_done` | `workflowStepName: "SUCCESS"` |
| 9 | `dssf_workflow_completed` | `terminalAction: "LOGOUT"` |

After `dssf_workflow_completed`, the SDK closes with `CLOSED_BY_USER_SIGNED_OUT`. If the user leaves the flow without closing their account, the sequence ends with `dssf_workflow_closed` instead, and the SDK does not sign the user out.

### AstroPay Card

| Event Name | Category | Description | Added |
|------------|----------|-------------|-------|
| `btn_card_active_continue` | `user_action` | User tapped "Continue" after their card order became active | 2026-09-16 |
| `btn_card_more_actions_close` | `user_action` | User closed the "more actions" modal (X icon, backdrop click, or Escape key) | 2026-09-16 |
| `btn_card_more_actions_item` | `user_action` | User tapped an item inside the "more actions" modal. `eventProperties: { actionKey }` | 2026-09-16 |
| `btn_card_show_more_actions` | `user_action` | User tapped the "more actions" (...) button on the AstroPay Card home screen | 2026-09-16 |
| `btn_card_request_get_card` | `user_action` | User tapped "Get card" to start the card request flow | 2026-09-16 |
