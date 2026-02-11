---
name: testing
description: Sandbox environment, test credentials, and testing strategies for DEUNA
metadata:
  tags: testing, sandbox, test-credentials, qa, integration-testing
---

# Testing

## Sandbox environment

| Property | Value |
|---|---|
| Base URL | `https://api.sandbox.deuna.io` |
| Web SDK CDN | `https://cdn.deuna.io/web-sdk/v1.3/index.js` |
| SDK env parameter | `"sandbox"` |
| Read rate limit | 25 RPS |
| Write rate limit | 25 RPS |

## Test setup

1. Obtain **sandbox** API keys from your DEUNA TPM
2. Set your SDK environment to `"sandbox"`
3. Point API requests to `https://api.sandbox.deuna.io`

## Testing strategy

### Order lifecycle testing

1. Create an order → verify `200` response with `order_token`
2. Attempt payment → verify success/failure callbacks
3. Test capture on authorized transactions
4. Test void on authorized transactions
5. Test full and partial refunds
6. Test order expiration

### Widget testing

1. Initialize SDK with sandbox credentials
2. Test Payment Widget with different `paymentMethods` configurations
3. Test Checkout Widget with `PAYMENT-LINK` order type
4. Verify all callbacks fire correctly: `onSuccess`, `onError`, `onClosed`, `onCardBinDetected`
5. Test embedded mode (`target` parameter) vs modal mode

### Webhook testing

1. Set up a publicly accessible endpoint (use ngrok or similar for local development)
2. Verify signature validation passes for legitimate events
3. Verify signature validation rejects tampered payloads
4. Test handling of all payment status transitions
5. Ensure your handler is idempotent (duplicate events should not cause issues)

### Error handling testing

1. Send requests with missing headers → verify `EMA-1000`
2. Send invalid API key → verify `EMA-5000` / `EMA-5001`
3. Attempt payment on expired order → verify `EMA-6019`
4. Attempt duplicate refund → verify `DP-6005`
5. Exceed rate limits → verify `429` response and retry logic

## Load testing

- **Do not load test in sandbox** — lower rate limits will cause false failures
- Simulate requests with reasonable latency for throughput testing
- For complex integrations requiring load testing windows or rate limit increases, contact your DEUNA TPM or Account Manager

## Postman collection

DEUNA provides a Postman collection for manual API testing: https://docs.deuna.com/reference/access-postman
