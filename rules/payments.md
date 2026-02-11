---
name: payments
description: Purchase, authorize, capture, void, and refund flows for DEUNA
metadata:
  tags: payments, purchase, authorize, capture, void, refund, credit-card, processing
---

# Payment Processing

All payment operations require a previously created order token. See [orders.md](./orders.md) for order creation.

## Create purchase

**Endpoint:** `POST /merchants/transactions/purchase`

**Base URLs:**
- Sandbox: `https://api.sandbox.deuna.io/merchants/transactions/purchase`
- Production: `https://api.deuna.io/merchants/transactions/purchase`

**Required headers:**
- `X-API-KEY`: Your **public** API key
- `Authorization`: `Bearer <user_token>`
- `x-device-id`: Device identifier (for fraud detection). See https://docs.deuna.com/v2.0/docs/obtener-fraud-ids
- `accept`: `application/json`

**Required body fields:**
- `token` (string): Order token from order creation
- `email` (string): User email
- `method_type` (string enum): Payment method - `credit_card`, `cash`, `pos`, `voucher`, `bank_transfer`, `reference`
- `store_code` (string): Store code (use `"all"` for single-store setups)

**Optional body fields:**
- `credit_card` (object): Card details (number, exp_month, exp_year, cvv, card_holder). Not needed for cash/POS
- `card_id` (string): Tokenized card ID. If provided, only send `cvv` in `credit_card`
- `billing_address` (object): Customer billing address
- `shipping_address` (object): Customer shipping address
- `processor_name` (string enum): Payment processor name (e.g., `kushki`, `conekta_credit`, `paymentez_credit`, etc.)
- `browser_details` (object): Browser details for 3DS
- `callback_url` (string): Redirect URL
- `initiator_type` (enum): `merchant` (MIT) or `customer` (CIT)
- `market_type` (enum): `ecommerce` (default) or `moto` (Mail Order/Telephone Order)
- `metadata` (object): Custom key-value data
- `save_user_info` (boolean): Whether to create/save user
- `recurrence` (object): Recurring payment setup
- `secure_authentication` (object): 3DS authentication data

**Example:**

```bash
curl --request POST \
  --url https://api.sandbox.deuna.io/merchants/transactions/purchase \
  --header 'X-API-KEY: your-public-api-key' \
  --header 'Authorization: Bearer user-token' \
  --header 'x-device-id: device-123' \
  --header 'content-type: application/json' \
  --data '{
    "token": "order-token-here",
    "email": "customer@example.com",
    "method_type": "credit_card",
    "store_code": "all",
    "credit_card": {
      "card_number": "4111111111111111",
      "exp_month": 12,
      "exp_year": 2028,
      "cvv": "123",
      "card_holder": "John Doe"
    }
  }'
```

**Response codes:**
- `200` - Payment completed
- `422` - Bad request with processor error message

## Capture authorization

**Endpoint:** `POST /merchants/orders/{order_token}/capture`

Completes a transaction that was previously authorized (status `authorized`). Captures the reserved funds.

**Headers:** `X-API-KEY` (private key)

**Body:**
- `amount` (number, required): Amount in **cents** (e.g., $10.00 = `1000`)
- `order` (object, optional)

**Response codes:** `204` No Content (success), `401`, `403`, `409`

> The capture amount cannot exceed the originally authorized amount.

## Cancel authorization (void)

**Endpoint:** `POST /merchants/orders/{order_token}/void`

Releases/unfreezes the funds of an authorized transaction.

**Headers:** `X-API-KEY` (private key)

**Body:**
- `order` (object, optional)
- `reason` (string, optional)

**Response codes:** `204` No Content (success), `401`, `404`, `409`

## Refund payment

**Endpoint:** `POST /merchants/orders/{order_token}/refund`

Refunds a completed order. Supports both full and partial refunds.

**Headers:** `X-API-KEY` (private key)

**Body:**
- `reason` (string, required): Reason for the refund
- `amount` (number, optional): Amount in **cents** for partial refund. Omit for full refund
- `order` (object, optional)

**Response codes:** `200` OK, `400`, `401`, `500`

> DEUNA allows X quantity of partial refunds where the sum of all partial amounts equals the initial payment amount. Each partial refund triggers a webhook notification.

## V2 endpoints

DEUNA also provides V2 versions of purchase, capture, and refund with asynchronous processing:
- `POST /merchants/transactions/v2/purchase` - Create V2 purchase
- `POST /merchants/orders/{order_token}/v2/capture` - Capture V2
- `POST /merchants/orders/{order_token}/v2/refund` - Refund V2

Use V2 endpoints when you need asynchronous settlement handling. For details, fetch: https://docs.deuna.com/reference/purchasev2

## Payment method types

| method_type | Description |
|---|---|
| `credit_card` | Credit/debit card payment |
| `cash` | Cash payment (e.g., OXXO) |
| `pos` | Point-of-sale terminal |
| `voucher` | Voucher/wallet payment |
| `bank_transfer` | Bank transfer (e.g., SPEI, PIX) |
| `reference` | Reference-based payment |

## Verify payment with OTP

**Endpoint:** `POST /merchants/transactions/verify-otp`

Some payment methods (e.g., Nequi) require OTP verification to complete the transaction.

## Important notes

- All amounts are in **cents** (e.g., $10.00 = `1000`)
- Processing time varies by processor, acquirer, and anti-fraud provider
- The 60-second timeout covers all third-party vendor cases
- For `credit_card` method: if using a tokenized `card_id`, only send the `cvv` field
- The `store_code` field is required; use `"all"` for single-store merchants
