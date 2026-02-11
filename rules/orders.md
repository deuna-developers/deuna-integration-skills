---
name: orders
description: Order creation, tokenization, lifecycle, and management in DEUNA
metadata:
  tags: orders, tokenize, order-token, create-order, lifecycle
---

# Orders

Orders are the core entity in DEUNA. You must create (tokenize) an order before processing a payment or initializing any widget.

## Create order (tokenize)

**Endpoint:** `POST /merchants/orders`

**Environments:**
- Sandbox: `https://api.sandbox.deuna.io/merchants/orders`
- Production: `https://api.deuna.io/merchants/orders`

**Headers:**
- `X-API-KEY` (required): Your **private** API key
- `Content-Type`: `application/json`
- `accept`: `application/json`

**Required fields at order level:**
- `currency` (string): Currency code matching your merchant configuration (e.g., `"MXN"`, `"COP"`, `"USD"`)
- `store_code` (string): Store code — use `"all"` for single-store setups
- `total_amount` (number): Total order amount in **cents** (required)
- `sub_total` (number): Subtotal in **cents** (required)
- `items_total_amount` (number): Sum of item amounts in **cents** (required)
- `shipping_amount` (number): Shipping cost in cents (use `0` if none)
- `total_tax_amount` (number): Total tax in cents (use `0` if none)
- `total_discount` (number): Total discount in cents (use `0` if none)
- `items` (array): Order line items (see structure below)
- `billing_address` (object): Customer billing address (see structure below)
- `shipping_address` (object): Customer shipping address (recommended)

> **Without `total_amount`, `sub_total`, and `items_total_amount` DEUNA returns error EMA-1002.**

**Request body:**

```json
{
  "order": {
    "store_code": "all",
    "currency": "MXN",
    "total_amount": 1000,
    "sub_total": 1000,
    "items_total_amount": 1000,
    "shipping_amount": 0,
    "total_tax_amount": 0,
    "total_discount": 0,
    "items": [
      {
        "id": "SKU-001",
        "name": "Product Name",
        "description": "Product description",
        "quantity": 1,
        "price": 1000,
        "taxable": true,
        "unit_price": {
          "amount": 1000,
          "original_amount": 1000,
          "discount_amount": 0,
          "currency": "MXN",
          "currency_symbol": "$"
        },
        "total_amount": {
          "amount": 1000,
          "original_amount": 1000,
          "discount_amount": 0,
          "currency": "MXN",
          "currency_symbol": "$"
        },
        "tax_amount": {
          "amount": 0,
          "original_amount": 0,
          "discount_amount": 0,
          "currency": "MXN",
          "currency_symbol": "$"
        }
      }
    ],
    "billing_address": {
      "first_name": "Juan",
      "last_name": "Pérez",
      "email": "juan@example.com",
      "phone": "+5215512345678",
      "address1": "Av. Reforma 222",
      "city": "Ciudad de México",
      "zipcode": "06600",
      "state_name": "CDMX",
      "country": "MX",
      "country_code": "MX",
      "address_type": "home",
      "is_default": true
    },
    "shipping_address": {
      "first_name": "Juan",
      "last_name": "Pérez",
      "email": "juan@example.com",
      "phone": "+5215512345678",
      "address1": "Av. Reforma 222",
      "city": "Ciudad de México",
      "zipcode": "06600",
      "state_name": "CDMX",
      "country": "MX",
      "country_code": "MX",
      "address_type": "home",
      "is_default": true
    }
  },
  "order_type": "DEUNA-CHECKOUT"
}
```

> **Country codes must be ISO 3166-1 alpha-2** (e.g., `"MX"` not `"MEX"`, `"CO"` not `"COL"`).

**Order types** (use hyphens, not underscores):
- `DEUNA-CHECKOUT` (default) - Standard checkout flow
- `DEUNA-NOW` - Express payment
- `PAYMENT-LINK` - Payment link flow (required for Checkout Widget)
- `AIRLINE-ORDER` - Airline order (requires `airline_information` object)

> **IMPORTANT:** Order types use **hyphens** (`DEUNA-NOW`), not underscores (`DEUNA_NOW`). Using underscores may cause the widget to not display payment methods.

**Response codes:**
- `200` - Order tokenized successfully
- `400` - Bad request (e.g., missing X-API-Key)
- `401` - Unauthorized (invalid API key or Bearer auth)
- `409` - Conflict (invalid payment method, processors not available, invalid processor)
- `500` - Internal error (cannot create order)

**Response structure:**

The order token is at `response.token` (root level, not nested under `order`):

```json
{
  "token": "ORDER_TOKEN_HERE",
  "order_type": "DEUNA_CHECKOUT",
  "order": {
    "order_id": "ord_abc123",
    "status": "pending",
    "currency": "MXN",
    "total_amount": 1000,
    "token": ""
  }
}
```

Use `response.token` (root level) as the `orderToken` for widgets and subsequent API calls. Note: `order.token` exists in the response but is an empty string and should not be used.

## Get order

**Endpoint:** `GET /merchants/orders/{order_token}`

Retrieve the current state of an order using its token.

## Update order

**Endpoint:** `PATCH /merchants/orders/{order_token}`

Update order fields (e.g., items, amounts, addresses) before payment.

## Expire order

**Endpoint:** `POST /merchants/orders/{order_token}/expire`

Manually expire an order. An order can only be expired if:
- It has no associated payment attempt
- Its status is `pending`

> **Note:** For PIX payments, an order can be expired when it has no associated payment attempt or the attempt is still pending.

## Order lifecycle

1. **Created/Tokenized** - Order is created with items and amounts
2. **Pending** - Awaiting payment processing
3. **Processing** - Payment is being processed
4. **Succeeded** - Payment completed successfully
5. **Cancelled** - Manually cancelled or refunded
6. **Expired** - Order expired (via API or timeout)
7. **Refunded** - Full or partial refund processed

## Obtain order payment methods

**Endpoint:** `GET /merchants/transactions/stores/{store_code}/payment-methods`

Returns the available payment methods configured for a specific store.

## Important notes

- Amounts are expressed in **cents** (e.g., $10.00 = `1000`)
- The `order_token` returned from creation is used across all subsequent operations (payments, widgets, webhooks)
- Always use the private API key (`X-API-KEY`) for server-side order operations
- For the Checkout Widget, create the order with `order_type: "PAYMENT-LINK"` and do NOT set a `redirect_url` in the order
- **Currency and country must match** your merchant configuration in DEUNA. If no payment methods are configured for the currency you send, the widget will appear empty without any error
- `total_amount`, `sub_total`, and `items_total_amount` are **required** — omitting them causes error `EMA-1002`
- Include `shipping_amount`, `total_tax_amount`, and `total_discount` (set to `0` if not applicable) to avoid unexpected behavior
- Country codes use **ISO 3166-1 alpha-2** format: `"MX"` (not `"MEX"`), `"CO"` (not `"COL"`)
- Each item should include `id`, `name`, `description`, `price`, and the full amount sub-objects (`original_amount`, `discount_amount`)
- Always include `store_code` in the order (use `"all"` for single-store merchants)
