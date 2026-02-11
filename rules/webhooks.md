---
name: webhooks
description: Webhook events, dynamic webhooks, order/payment statuses, and signature verification for DEUNA
metadata:
  tags: webhooks, events, signatures, notifications, hmac, order-status, payment-status
---

# Webhooks

DEUNA webhooks provide real-time push notifications when events occur in the order lifecycle.

## How webhooks work

Webhooks communicate events between DEUNA and your application. They notify your system about:

**During buyer information collection:**
- Address collection from e-commerce
- Shipping method update
- Coupon applied to order
- Coupon removed from order

**Order status changes:**
- Pending order
- Order completed (succeeded)
- Order cancelled
- Order refunded
- Order expired
- Order pending approval

## Before you start

1. Implement endpoints on your server to receive DEUNA webhook payloads
2. Keep order information up-to-date throughout the lifecycle
3. Implement endpoints for buyer info collection: address validation, shipping method updates, coupon operations
4. Use the standard response format provided by DEUNA

## Payment statuses

| Status | Description | Sub-statuses |
|---|---|---|
| `pending` | Payment initiated but not confirmed | `processing`, `pending`, `denied`, `authorizing`, `manual_review`, `pending_3ds` |
| `succeeded` | Payment completed | `processed`, `authorized`, `captured`, `partial_refunded` |
| `cancelled` | Order cancelled | `cancelled`, `voided`, `refunded` |
| `expired` | Order expired | No payment associated, or payment still pending |

Access payment status at: `order.payment.data.status`

### Pending sub-statuses
- `processing` - Order is still being processed
- `pending` - Pending due to business rules or processor hasn't confirmed
- `denied` - Order was denied
- `authorizing` - In authorization process
- `manual_review` - Manually reviewed by payment/fraud provider
- `pending_3ds` - Pending 3DS authentication

### Succeeded sub-statuses
- `processed` - Order was processed
- `authorized` - Order was authorized (funds reserved)
- `captured` - Order has been captured
- `partial_refunded` - Partial refund applied

> DEUNA allows multiple partial refunds where the sum equals the initial payment. Each partial refund triggers a webhook.

> Pending orders are considered complete when the actual payment method has been used to complete them. Flag orders according to their status to preserve idempotency.

## Webhook signature verification

DEUNA uses HMAC-SHA256 signatures. The signature is in the `X-Deuna-Signature` header.

### Verification steps

1. Extract `X-Deuna-Signature` header from the incoming request
2. Generate expected signature using your **private API key** and the raw request body
3. Compare generated signature with received signature
4. Check the `signed_at` timestamp for freshness

> Use the **raw body** of the request. Any framework manipulation of the body will cause validation to fail.

### Python example

```python
import hmac
import hashlib
import base64
from django.http import HttpResponse

def validate_hmac_sha256_signature(secret, payload, signature):
    secret_bytes = bytes(secret, 'utf-8')
    payload_bytes = bytes(payload, 'utf-8')
    h = hmac.new(secret_bytes, payload_bytes, hashlib.sha256)
    expected_signature = base64.b64encode(h.digest()).decode('utf-8')
    return hmac.compare_digest(signature, expected_signature)

secret = "<your-private-api-key>"

@csrf_exempt
def my_webhook_view(request):
    payload = request.body
    sig_header = request.META['X-Deuna-Signature']

    if not validate_hmac_sha256_signature(secret, payload, sig_header):
        return HttpResponse(status=403)

    return HttpResponse(status=201)
```

### Node.js example

```javascript
const crypto = require('crypto');

function validateSignature(secret, payload, signature) {
  const hmac = crypto.createHmac('sha256', secret);
  hmac.update(payload);
  const expectedSignature = hmac.digest('base64');
  return crypto.timingSafeEqual(
    Buffer.from(signature),
    Buffer.from(expectedSignature)
  );
}
```

## Dynamic webhooks

Dynamic webhooks allow adding custom webhook functionality during checkout without code changes.

### Configure a dynamic webhook

```
POST api/v1/merchants/{merchant_id}/webhooks
Header: {merchant_token}
```

```json
{
  "event_name": "string",
  "url": "string",
  "fields_update": {
    "fields": ["string"],
    "custom_fields": ["string"]
  },
  "response_body": {
    "fields": ["string"],
    "custom_fields": ["string"]
  },
  "response_template": "string",
  "response_data": "string"
}
```

**Payload fields:**
- `event_name`: Name of the webhook
- `url`: URL to receive the webhook request
- `fields_update.fields`: Order fields modified when webhook executes
- `fields_update.custom_fields`: Custom order fields modified
- `response_body.fields`: Order fields returned to frontend
- `response_body.custom_fields`: Custom fields returned to frontend
- `response_template`: Go template to transform merchant response into DEUNA order format

### Response template

Uses [Go Templates](https://pkg.go.dev/text/template) to transform your store's order format into DEUNA's format:

```json
{
  "order": {
    "order_id": "{{ .result.uuid }}",
    "custom_fields": { "business_id": "{{ .result.business_id }}" }
  }
}
```

For more template examples, see: https://github.com/DUNA-E-Commmerce/merchant-templates

### Practical use cases
- **Add tips**: Customize price display with a tip field
- **Add tax to cash orders**: Apply tax percentage for cash payment methods
- **Points and coupons**: Execute payments with loyalty points, donations, and coupons
