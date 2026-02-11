---
name: sdk-mobile
description: iOS, Android, and React Native SDK integration for DEUNA
metadata:
  tags: sdk, mobile, ios, android, react-native, swift, kotlin
---

# Mobile SDKs

DEUNA provides native SDKs for iOS, Android, and React Native. The mobile SDKs follow the same patterns as the Web SDK: initialize with a public API key, then display Payment Widget, Checkout Widget, or Card Vault.

## Available SDKs

| Platform | Documentation |
|---|---|
| iOS (Swift) | https://docs.deuna.com/reference/ios-sdk |
| Android (Kotlin) | https://docs.deuna.com/reference/android-sdk |
| React Native | https://docs.deuna.com/reference/reactnative-sdk |

## General integration pattern

All mobile SDKs follow this flow:

1. **Install the SDK** via the platform's package manager (CocoaPods/SPM for iOS, Gradle for Android, npm for React Native)
2. **Initialize** with your public API key and environment (`sandbox` or `production`)
3. **Create an order** on your backend using the DEUNA API (see [orders.md](./orders.md))
4. **Pass the `orderToken`** to the SDK widget
5. **Listen to callbacks** for success, error, and close events
6. **Handle the response** to update your UI

## Web SDK in a WebView

If you prefer not to use native SDKs, DEUNA also supports rendering the Web SDK inside a WebView. This provides a unified approach across platforms.

See: https://docs.deuna.com/reference/unified-webview-payment

## Important notes

- Always create orders on your **backend** (server-side) using the private API key
- Only the `orderToken` should be passed to the client/mobile app
- The public API key is used to initialize the SDK
- For the latest SDK versions, installation steps, and platform-specific configuration, always fetch the live documentation links above
