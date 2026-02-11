---
name: security
description: PCI compliance, HMAC signatures, and data handling best practices for DEUNA
metadata:
  tags: security, pci, compliance, hmac, signatures, data-handling
---

# Security

## API key management

- **Private API key**: Server-side only. Used for order creation, payment operations, refunds. Never expose in client-side code.
- **Public API key**: Client-side. Used for SDK initialization. Safe to include in frontend code.

## Webhook signature verification

DEUNA signs all webhook events with HMAC-SHA256. Always verify signatures before processing webhooks.

1. Extract `X-Deuna-Signature` header
2. Compute HMAC-SHA256 of the raw request body using your private API key
3. Base64-encode the result
4. Compare using constant-time comparison (to prevent timing attacks)
5. Validate `signed_at` timestamp to prevent replay attacks

See [webhooks.md](./webhooks.md) for full verification code examples.

## Data handling

- All amounts are in **cents** — never store or transmit floating-point currency values
- Never log full card numbers, CVVs, or sensitive payment data
- Use tokenized `card_id` for returning customers instead of storing card details
- The `save_user_info` flag in purchase requests creates a DEUNA user profile for card tokenization

## PCI compliance

- DEUNA's SDK widgets handle card collection — your servers never touch raw card data when using widgets
- When using the API directly with `credit_card` object, ensure your backend meets PCI-DSS requirements
- Use the Payment Widget or Checkout Widget to stay out of PCI scope

## Fraud prevention

- Pass `x-device-id` header in purchase requests for fraud detection
- Configure `fraudCredentials` in the SDK for anti-fraud providers (e.g., Riskified)
- See https://docs.deuna.com/v2.0/docs/obtener-fraud-ids for generating fraud IDs
- Anti-fraud error code `FRD-7000` indicates fraud score exceeded the threshold

## Rate limiting

- HTTP 429 responses indicate rate limit exceeded — implement exponential backoff
- Never retry without backoff to avoid account-level throttling
- See [getting-started.md](./getting-started.md) for rate limit details

## Best practices

1. Always verify webhook signatures before acting on events
2. Use idempotent order handling — check order status before processing
3. Set client-side timeouts > 60 seconds to match DEUNA's API timeout
4. Implement retry logic with exponential backoff for transient failures
5. Store and validate `order_token` on your backend — never trust client-provided tokens without verification
6. Use HTTPS for all webhook endpoints
7. Handle unknown webhook event types gracefully (ignore, don't reject)
