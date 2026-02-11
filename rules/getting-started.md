---
name: getting-started
description: Authentication, environments, rate limits, and initial setup for DEUNA
metadata:
  tags: setup, authentication, api-keys, environment, configuration, rate-limits
---

# Getting Started with DEUNA

## Authentication

DEUNA APIs are authenticated via the `X-API-KEY` header provided by your DEUNA TPM (Technical Partner Manager).

There are two types of API keys:
- **Private API key**: Used for server-side operations (create orders, process payments, refunds, captures, voids)
- **Public API key**: Used for client-side SDK initialization

```
X-API-KEY: your-api-key-here
```

> If you experience delays obtaining credentials, contact support@deuna.com.

## Environments

| Environment | Base URL | Purpose |
|---|---|---|
| Sandbox | `https://api.sandbox.deuna.io` | Testing and integration development |
| Production | `https://api.deuna.io` | Live transactions |

- **Sandbox**: Use test credentials to integrate and test all services
- **Production**: Use production credentials for real transactions

## Timeout

DEUNA uses a **60-second timeout** for all APIs. The total processing time includes:
1. DEUNA processing time
2. Processor/acquirer processing time
3. Anti-fraud provider processing time

> It is recommended to set your client timeout **longer than 60 seconds** to account for the full transmission process.

## Rate limits

Rate limits are applied at the **account level**, regardless of which API key is used.

| Operation | Production | Sandbox |
|---|---|---|
| Read operations | 100 RPS | 25 RPS |
| Write operations | 100 RPS | 25 RPS |

- Exceeding limits returns **HTTP 429** - safe to retry with exponential backoff
- Data/reporting products are excluded from rate limits
- **Do not load test in sandbox** due to lower rate limits
- For custom rate limit adjustments, contact your DEUNA TPM or Account Manager

## API versioning

DEUNA classifies these as **backwards-compatible** changes (no new version needed):
- Adding new resources, optional request parameters, or properties to responses
- Changing property order in responses
- Adjusting length/format of opaque strings (identifiers, error messages)
- Introducing new event types (ensure your webhook handler ignores unknown types)

When a **breaking change** is made, DEUNA releases a new API version so you can upgrade at your convenience.

## Quick start flow

1. Obtain your API keys from your DEUNA TPM
2. Create an order: `POST /merchants/orders` (see [orders.md](./orders.md))
3. Process payment either via:
   - **API**: `POST /merchants/transactions/purchase` (see [payments.md](./payments.md))
   - **Web SDK**: Initialize widget with `orderToken` (see [sdk-web.md](./sdk-web.md))
4. Handle webhooks for order status updates (see [webhooks.md](./webhooks.md))

## Postman collection

DEUNA provides a Postman collection for API testing. See: https://docs.deuna.com/reference/access-postman

## IP addresses

For IP whitelisting requirements, see: https://docs.deuna.com/reference/ip-addresses
