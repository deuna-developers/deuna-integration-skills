---
description: Checkout SDK Events — list of events emitted by the Checkout SDK with trigger condition and payload. Use when working with SDK event listeners, analytics, or subscribing to checkout events.
globs:
alwaysApply: false
---

# Checkout SDK Events

Events are received via the `onEventDispatch` callback, where `event` is the event name and `payload` is the event payload:

```js
onEventDispatch: (event, payload) => {
  console.log("Event:", event, payload);

  if(event === "checkoutStarted"){
    console.log("checkout has been started");
  }
},
```

## Core
`checkoutStarted` — payment widget renders successfully
`paymentProcessing` — user clicks pay and payment starts processing | `{ "processorName": "" }`
`appCrash` — error occurs and widget cannot be displayed
`checkoutFailed` — error occurs while loading the payment widget
`purchase` — payment successfully processed (backend)
`vaultSaveSuccess` — card saved successfully
`continueTo` — user clicks a "continue" button | `{ "processorName": "" }`
`vaultRedirect3DS` — vault 3DS redirect is displayed
`adBlock` — ad blocker detected in payment view
`couponEntered` — user enters a coupon
`linkStarted` — payment link loads successfully
`linkFailed` — error loading the payment link
`linkCriticalError` — critical error in the payment link
`linkNonCriticalError` — non-critical error in the payment link
`purchaseError` — error during purchase
`purchaseRejected` — payment rejected
`rejected` — payment rejected by processor
`LowerTagShown` — lower tag is displayed
`UpperTagShown` — upper tag is displayed
`tagShown` — a tag is displayed
`localizeMissingTranslationKey` — translation key not found in remote translations | `{ "params": { "env": "", "lang": "", "projectName": "", "chunk": "", "defaultTranslations": "JSON", "customStylesTranslations": "JSON" }, "payload": { "key": "" } }`
`localizeMissingDefaultTranslationKey` — translation key not found in code-defined translations (same shape as localizeMissingTranslationKey)
`localizeFetchTranslationsError` — remote translations could not be fetched | `{ "params": { "env": "", "lang": "", "projectName": "", "chunk": "", "defaultTranslations": "JSON", "customStylesTranslations": "JSON" }, "payload": { "error": "JSON" } }`

## Vault
`vaultStarted` ⚠️ DEPRECATED — vault modal or link opens
`vaultClosed` — user clicks X to close vault
`vaultProcessing` — processing screen for saving card is displayed
`vaultSaveClick` — user clicks Save to store a card
`vaultSaveError` — error while saving the card
`vaultServerError` — server error in vault
`vaultFailed` — vault widget cannot be displayed

## Payment Methods
`paymentMethodsEntered` — payment method entry is detected
`paymentMethodsStarted` — payment methods section is displayed
`paymentClick` — user clicks Pay button
`paymentMethodsAddCard` — user selects to add a new card | `{ "processorName": "" }`
`paymentMethodsChangeCard` — user switches cards
`paymentMethodSplitPayments` — user presses SplitPaymentButton
`paymentMethodsChangeAmountInitiated` — user starts editing an amount
`paymentMethodsChangeAmountEntered` — user stops interacting with amount input
`paymentMethodsCardDeleted` — user clicks delete on a saved card | `{ "id": "", "card_type": "", "company": "", "country_iso": "", "is_default": false, "is_expired": false }`
`onBinDetected` — BIN of entered card is detected | `{ "processorName": "" }`
`onInstallmentSelected` — user selects an installment option (also fires on load, manual change, split recalc) | `{ "processorName": "" }`
`paymentMethodsCardNumberEntered` — user completes card number field | `{ "processorName": "" }`
`paymentMethodsCardNumberInitiated` — user starts typing card number
`paymentMethodsCardNameEntered` — user completes cardholder name field | `{ "processorName": "" }`
`paymentMethodsCardNameInitiated` — user starts typing cardholder name
`paymentMethodsCardExpirationDateEntered` — user completes expiration date field | `{ "processorName": "" }`
`paymentMethodsCardExpirationDateInitiated` — user starts typing expiration date
`paymentMethodsCardSecurityCodeEntered` — user completes CVV field | `{ "processorName": "" }`
`paymentMethodsCardSecurityCodeInitiated` — user starts typing CVV
`paymentMethodsCardIdentityNumberEntered` — user completes identity document field
`paymentMethodsCardIdentityNumberInitiated` — user starts typing identity document
`paymentMethodsCardSelected` — user selects an existing saved card | `{ "processorName": "" }`
`paymentMethodsSelected` — user confirms selected payment method
`paymentMethodsShowMyCards` — user views list of saved cards
`onInstallmentsDeselected` — user deselects installments
`selectPaymentMethod` — user selects a payment method
`backTo` — user clicks Back; in split flow includes | `{ "metadata": { "step": "single_payment_step" } }`
`paymentMethodsAccountSelectedPayPal` — user selects a PayPal account
`paymentMethodsAddAccountPayPal` — user adds a new PayPal account
`paymentMethodsShowMyAccountsPayPal` — user clicks "View all my accounts" in PayPal
`paymentMethodsDeleteAccountPayPal` — PayPal account deletion completed
`paymentMethodsDeleteAccountInitiatedPayPal` — user initiates PayPal account deletion
`paymentMethodsDeleteAccountConfirmedPayPal` — user confirms PayPal account deletion
`paymentMethodsDeleteAccountCancelledPayPal` — user cancels PayPal account deletion
`IdentityDocumentEntered` ⚠️ DEPRECATED — user enters identity document
`IdentityDocumentInitiated` ⚠️ DEPRECATED — user starts typing identity document

## Billing Address
`billingSet` — user has a billing address from past purchases | `{ "showInMainView": false, "cardIndex": 1 }`
`billingChange` — user clicks to change billing address | `{ "metadata": { "card_index": "<current step>" } }`
`billingFormSave` — user clicks Save on billing address form
`billingStarted` — user clicks to add billing data (no prior data) | `{ "card_index": "currentStepIndex + 1", "show_in_main_view": "true | false" }`

## APM
`apmLoadingiframe` — APM iframe is loading
`apmBankSelected` — user selects a bank from APM list
`apmPaymentClick` — user clicks pay with APM
`apmPaymentMethodSelected` — user selects a payment method in Nu payment experience
`apmOtpStarted` — APM OTP input is displayed
`apmOtpAccepted` — user enters correct APM OTP
`apmOtpRejected` — APM OTP is rejected
`apmOtpResend` — user requests OTP resend
`apmRedirect` — user is redirected to APM interface
`apmClickRedirect` — user clicks to be redirected to APM
`apmPaymentError` — error during APM payment processing
`apmSaveId` — user downloads APM QR code or code
`apmCopyId` — user copies the transaction ID
`apmDocIdEntered` — user enters identity document in APM flow
`apmDocIdInitiated` — user starts typing identity document in APM flow
`apmPhoneEntered` — user enters phone number in APM flow
`apmPhoneInitiated` — user starts typing phone number in APM flow
`apmReadInstructions` — user reads payment instructions in APM flow
`apmReferenceShown` — reference view shown for reference-based APMs (PIX, Nequi, CoDi)
`apmConsentResponse` — response to user consent received | `{ "status": "success | error" }`
`ampGoToThankyouPage` — user is redirected to thank-you page
`onInstallmentSelectedPayPal` — user selects to pay with installments via PayPal
`paypalAccountCreated` — PayPal account successfully linked
`paypalAccountCreateFailed` — PayPal account linking fails

## 3DS
`paymentMethods3dsInitiated` — 3DS flow starts after receiving pending_3ds from backend
`paymentMethodsEmbeddedLoaded` — embedded/iframe 3DS container confirms load or load failure | `{ "processorName": "", "methodType": "", "mode": "embedded", "status": "error", "errorMessage": "" }`
`three_ds_authentication_started` — 3DS authentication is initiated (backend)
`three_ds_challenge_issued` — 3DS challenge issued to user (backend)
`three_ds_authentication_completed` — user completes 3DS authentication (backend)
`three_ds_method_completed` — 3DS authentication method completes (backend)
`three_ds_authentication_failed` — 3DS authentication fails (backend)

## Login
`login` — user initiates login process
`loginStarted` — login view is displayed
`loginEmailInitiated` — user starts typing their email
`loginEmailEntered` — user types and confirms their email
`loginEmailTooltip` — tooltip with email info is displayed
`loginOtpStarted` — OTP input for login is displayed
`loginOtpSendEmail` — OTP code sent via email
`loginOtpSendSms` — OTP code sent via SMS
`loginOtpAccepted` — user enters correct OTP code
`loginOtpRejected` — OTP code entered is rejected
`loginOtpCodeExpired` — OTP code expires
`loginOtpContinueAsGuest` — user skips OTP and continues as guest
`loginWithCrossDomain` — cross-domain authentication login is initiated

## User Info
`userInfoStarted` — user information section is displayed
`editContactDetails` — user clicks to edit contact details
`userInfoUpdateStarted` — user clicks to change user info
`userInfoFirstNameInitiated` — user starts typing first name
`userInfoFirstNameEntered` — user completes first name field
`userInfoLastNameInitiated` — user starts typing last name
`userInfoLastNameEntered` — user completes last name field
`userInfoPhoneInitiated` — user starts typing phone number
`userInfoPhoneEntered` — user completes phone number field
`userInfoIdentityDocumentInitiated` — user starts typing identity document
`userInfoIdentityDocumentEntered` — user completes identity document field
`userInfoUpdateIdentityDocumentInitiated` — user clicks identity document field for update
`userInfoUpdateIdentityDocumentEntered` — user completes identity document field during update
`userInfoAccountCreated` — user creates an account from the widget
`userInfoDataSaved` — user data saved successfully
`userInfoDataNotSaved` — error occurred and user data could not be saved

## Shipping Address
`shippingAddressStarted` — shipping address section is displayed
`shippingAddressSearchInitiated` — user starts typing an address
`shippingAddressSearchEntered` — user types an address in search field
`shippingMethodsAddressDetailsConfirmed` — user confirms address details
