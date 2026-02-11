---
name: sdk-web
description: Web SDK initialization, Payment Widget, Checkout Widget, and Card Vault for DEUNA
metadata:
  tags: sdk, web, javascript, widget, payment-widget, checkout-widget, card-vault, frontend
---

# Web SDK

DEUNA's Web SDK provides Payment Widget, Checkout Widget, and Card Vault components. The SDK is loaded via CDN only.

## Requirements

- Knowledge of web development with JavaScript
- Active DEUNA account
- Valid DEUNA credentials (public API key)

## 1. Install the SDK

Add the DEUNA SDK script to your HTML:

```html
<script crossorigin src="https://cdn.deuna.io/web-sdk/v1.3/index.js"></script>
```

Or load dynamically with JavaScript:

```javascript
if (typeof DeunaSDK === "undefined") {
  var script = document.createElement("script");
  script.src = "https://cdn.deuna.io/web-sdk/v1.3/index.js";
  script.onload = function() {
    // Initialize SDK and show widget
  };
  document.head.appendChild(script);
} else {
  // Show widget directly
}
```

> Stable version: 1.4

## 2. Initialize the SDK

```javascript
await DeunaSDK.initialize({
  publicApiKey: "YOUR_PUBLIC_API_KEY",
  env: "sandbox", // "sandbox" or "production"
});
```

## Payment Widget

The Payment Widget supports credit/debit cards, APMs (OXXO, KueskiPay, Aplazo, etc.), and wallets.

### Display the widget

```javascript
await DeunaSDK.initPaymentWidget({
  orderToken: "<DEUNA order token>", // Required
  userToken: "...",                   // Optional: Bearer token of DEUNA user
  styleFile: "...",                   // Optional: UUID for custom theme
  language: "en",                     // Optional: "en", "es", "pt"
  hidePayButton: false,               // Optional: hide default pay button
  target: "#my-container",            // Optional: CSS selector for embedded mode (default: modal)
  widgetExperience: {                 // Optional
    userExperience: {
      disableInstallments: true       // Optional: disable installment options
    }
  },
  fraudCredentials: {                 // Optional: required if fraud provider needs fingerprint
    RISKIFIED: {
      storeDomain: "yourdomain.com",
    },
  },
  paymentMethods: [                   // Optional: filter displayed payment methods
    { paymentMethod: "credit_card" },
    { paymentMethod: "voucher", processors: ["daviplata"] },
  ],
  callbacks: {
    onSuccess: (data) => {
      console.log("Payment success:", data);
    },
    onError: (error) => {
      console.log("Payment error:", error);
    },
    onClosed: (action) => {
      // action: "userAction" (user clicked X) or "systemAction" (programmatic close)
      console.log("Widget closed:", action);
    },
    onCardBinDetected: (payload) => {
      // payload is null when user clears card number field
      console.log("BIN detected:", payload);
    },
    onInstallmentSelected: (data) => {
      console.log("Installment selected:", data);
    },
    onEventDispatch: (event, payload) => {
      console.log("Event:", event, payload);
    },
    onResize: (height) => {
      // Adjust container height for embedded mode
    },
  },
});
```

### Payment methods priority

1. Methods passed in `paymentMethods` parameter (if configured and active at merchant level)
2. Methods in `include_payment_options` from the order creation
3. All methods configured at merchant level

If only one payment method is provided, the form opens directly without showing method buttons.

### Close the widget

```javascript
await DeunaSDK.close();
```

### Customize appearance

```javascript
await DeunaSDK.setCustomStyle({
  theme: {
    colors: {
      primaryTextColor: "#023047",
      backgroundSecondary: "#8ECAE6",
      backgroundPrimary: "#F2F2F2",
      buttonPrimaryFill: "#FFB703",
      buttonPrimaryHover: "#FFB703",
      buttonPrimaryText: "#000000",
      buttonPrimaryActive: "#FFB703",
    },
  },
  HeaderPattern: {
    overrides: {
      Logo: {
        props: {
          url: "https://your-logo-url.com/logo.png",
        },
      },
    },
  },
});
```

### Refresh order (refetchOrder)

Use `refetchOrder` to update the widget after modifying the order (e.g., applying promotions based on card BIN):

```javascript
await DeunaSDK.initPaymentWidget({
  orderToken: "<token>",
  callbacks: {
    onCardBinDetected: async (cardBinMetadata) => {
      if (cardBinMetadata?.cardBrand === "mastercard") {
        // Update order via API with discount
        const order = await DeunaSDK.refetchOrder();
        console.log("Updated order:", order);
      }
    },
  },
});
```

## Checkout Widget

The Checkout Widget provides a full checkout experience. Requires an order of type `PAYMENT-LINK`.

```javascript
await DeunaSDK.initCheckout({
  orderToken: "<DEUNA order token>", // Must be PAYMENT-LINK order type
  styleFile: "...",                   // Optional: theme UUID
  language: "en",                     // Optional
  callbacks: {
    onSuccess: async (data) => {
      await DeunaSDK.close();
      console.log("Checkout success:", data);
    },
    onError: (error) => {
      console.log("Checkout error:", error);
    },
    onClosed: (action) => {
      console.log("Checkout closed:", action);
    },
    onEventDispatch: (event, payload) => {
      console.log("Event:", event, payload);
    },
  },
});
```

> When creating the order for Checkout Widget, do NOT define a `redirect_url` — otherwise the `onSuccess` callback won't execute correctly.

## Card Vault Widget

For tokenizing and saving cards without processing a payment. See: https://docs.deuna.com/docs/card-vault-web

## Important notes

- The SDK can **only** be installed via CDN (no npm package)
- By default, widgets render in a **modal**. Use the `target` parameter to embed in a specific HTML element
- **Embedded mode warning:** The `target` (embedded) mode may not render reliably in SPA frameworks like Next.js or React due to lifecycle/hydration issues. If the widget doesn't appear in embedded mode, **use the default modal mode** (omit `target`) as a reliable fallback
- **Currency/country must match merchant config:** The currency and country in the order must match what is configured in your DEUNA merchant. If there are no payment methods configured for that currency, the widget will appear empty with no error
- The `userToken` email must match the `billing_address.email` from the order for saved cards to display
- For 3DS support, see: https://docs.deuna.com/docs/3ds-next-action-widget
- For rendering in a WebView, see: https://docs.deuna.com/reference/unified-webview-payment
