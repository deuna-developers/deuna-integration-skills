---
name: error-handling
description: Error code tables for DEUNA API, payments, orders, checkout, subscriptions
metadata:
  tags: errors, error-codes, retries, failure, troubleshooting
---

# Error Handling

## Error structure

DEUNA API returns errors in this format:

```json
{
  "error": {
    "code": "EMA-1000",
    "description": "x-api-key"
  },
  "data": null
}
```

## Common API errors (EMA-*)

| Code | Name | Description | Action |
|---|---|---|---|
| EMA-1000 | MissingHeaders | Request header missing/invalid | Verify headers are sent correctly |
| EMA-1001 | CannotGetMetadata | Cannot get metadata | Check metadata format |
| EMA-1002 | PayloadError | Request payload failed | Verify payload/body format |
| EMA-1004 | OrderAlreadyProcessed | Order already processed | Place a new order |
| EMA-1005 | OrderStatusNotValidForTransition | Invalid status transition | Check current transaction state |
| EMA-2000 | CannotParseData | Data processing error | Verify data format |
| EMA-3001 | OrderNotFound | Order not found | Validate order ID/store |
| EMA-3002 | DataBaseError | Database error | Retry later |
| EMA-4000 | RequestServiceError | Cannot connect to service | Verify request |
| EMA-4001 | RequestServiceTimeout | Request timeout | Retry request |
| EMA-5000 | Unauthorized | Not authorized | Check X-API-Key header |
| EMA-5001 | InvalidAPIKey | Invalid API key | Use a valid API key |
| EMA-6000 | RefundError | Refund failed | Verify request body |
| EMA-6001 | VoidError | Void failed | Verify request body |
| EMA-6002 | PendingPayment | Order pending/processing | Wait for status update |
| EMA-6003 | CaptureError | Capture error | Verify order exists and meets criteria |
| EMA-6004 | CannotCreateOrder | Cannot create order | Verify order data is correct |
| EMA-6019 | OrderExpired | Order expired | Create a new order |
| EMA-6020 | CannotExpireOrder | Cannot expire order | Create a new order |
| EMA-9001 | AttemptsExceededCode | Transaction attempts exceeded | Try another card/method |
| EMA-9998 | MerchantInternalError | Internal error | Retry or try another processor |

## Payment errors (DP-*)

| Code | Name | Description | Action |
|---|---|---|---|
| DP-3000 | ErrPayload | Payload error or empty | Verify payload fields |
| DP-4001 | ErrUnauthorized | Not authorized | Validate X-API-Key |
| DP-4002 | ErrCardTokenForbidden | Card banned | Card is on fraud blacklist |
| DP-4008 | ErrCardNotFound | Card not found | Check card number or card_id association |
| DP-4009 | ErrProcessorNotFound | Processor not found/enabled | Check Admin payment method config |
| DP-4017 | ErrInvalidCard | Card issues | Verify card details |
| DP-4018 | ErrInvalidCaptureAmount | Capture exceeds authorized | Capture amount <= authorized |
| DP-4019 | ErrTransactionInProcess | Transaction in process | Wait for completion |
| DP-4024 | ErrInvalidCredentials | Bad processor credentials | Verify credentials |
| DP-4026 | ErrPurchase | Purchase failure | Verify details and config |
| DP-4100 | ErrMissingCVV | CVV missing | Provide CVV |
| DP-4101 | ErrInvalidCVV | Invalid CVV | Use correct CVV |
| DP-4200 | ErrInternalServerErrorPSP | Processor internal error | Retry or try another processor |
| DP-4300 | ErrExpCard | Expiration date error | Check card expiration |
| DP-4303 | ErrNumberCard | Card number error | Validate card number |
| DP-4400 | ErrProcessing3DS | 3DS cannot start | Try another card/method |
| DP-4500 | ErrBankRejected | Issuer bank decline | Try another card/method |
| DP-5001 | ErrNotImplemented | Method not configured | Configure payment method |
| DP-5004 | ErrFraudEvaluate | Fraud evaluation failed | Review processor parameters |
| DP-6000 | ErrRequestAlreadyProcessed | Already processed | Use a different request |
| DP-6002 | ErrCannotFindTransaction | Transaction not found | Validate transaction ID |
| DP-6003 | ErrUnknownPayment | Payment may not be approved | Retry or try another processor |
| DP-6005 | ErrDuplicateRefund | Already refunded | Check transaction status first |
| DP-7011 | ErrWaitingOTP | Waiting for OTP | Wait for OTP code |
| DP-7100 | ErrWorkflowPurchase | Missing parameters | Review required payload fields |
| DP-8001 | ErrFraudValidation | Fraud error | Review processor parameters |
| DP-9999 | ErrUnknown | Generic error | Retry or contact support |
| DP-10000 | ErrContextCanceled | Timeout/context cancelled | Review timeouts, add retry logic |
| DP-11000 | ErrRequestTimeout | Request timed out | Increase timeout, optimize payload |

## Order errors (EM-*)

| Code | Name | Description | Action |
|---|---|---|---|
| EM-1001 | WithoutOrderIdError | Order ID missing | Provide valid order ID |
| EM-1002 | OrderNotExist | Order does not exist | Verify order identifier |
| EM-1003 | WithoutPaymentMethodError | Payment method missing | Set up payment method |
| EM-1005 | PaymentNotConfigured | Method not configured | Enable and configure method |
| EM-2000 | WithoutOrdenToken | Order token missing | Provide valid token |
| EM-4002 | OutOfStock | Item out of stock | Check inventory |

## Checkout errors (CHECKOUT-*)

| Code | Name | Description |
|---|---|---|
| CHECKOUT-1000 | CheckoutNotReady | Checkout not ready - verify settings |
| CHECKOUT-1001 | CheckoutNotReadyForShipping | Shipping method not configured |
| CHECKOUT-1002 | CheckoutNotReadyForNotify | Notification endpoint not configured |

## Anti-fraud errors (FRD-*)

| Code | Name | Description |
|---|---|---|
| FRD-7000 | FraudCodeScoreAndWorkflow | Fraud score exceeded max, stopped by workflow |

## Subscription errors (SUBS-*)

| Code | Name | Description |
|---|---|---|
| SUBS-10000 | BusinessErrorInvalidRequest | Invalid request format |
| SUBS-10001 | BusinessErrorPlanAlreadyExists | Plan already exists |
| SUBS-10003 | BusinessErrorPlanNotFound | Plan not found |
| SUBS-10004 | BusinessErrorSubscriptionNotFound | Subscription not found |
| SUBS-10006 | BusinessErrorSubscriptionNotActive | Subscription not active |

## Retry strategy

- **HTTP 429** (rate limit): Retry with exponential backoff
- **HTTP 5xx** (server errors): Retry with backoff (max 3 attempts)
- **EMA-4001** (timeout): Generate a new request
- **DP-4200** (PSP internal): Retry or try another processor
- **DP-10000** (context cancelled): Review timeout settings, add retry logic

For the full error code reference, fetch: https://docs.deuna.com/reference/manejo-de-errores
