---
name: api-reference
description: Quick reference for DEUNA REST API endpoints
metadata:
  tags: api, rest, endpoints, quick-reference
---

# API Quick Reference

## Base URLs

| Environment | URL |
|---|---|
| Sandbox | `https://api.sandbox.deuna.io` |
| Production | `https://api.deuna.io` |

## Authentication

All requests require the `X-API-KEY` header. Some payment endpoints also require `Authorization: Bearer <token>`.

## Orders

| Method | Endpoint | Description |
|---|---|---|
| POST | `/merchants/orders` | Create/tokenize order |
| GET | `/merchants/orders` | List orders |
| GET | `/merchants/orders/{order_token}` | Get order by token |
| PATCH | `/merchants/orders/{order_token}` | Update order |
| POST | `/merchants/orders/{order_token}/expire` | Expire order |
| GET | `/merchants/orders/{order_token}/payment-methods` | Get order payment methods |

## Payments

| Method | Endpoint | Description |
|---|---|---|
| POST | `/merchants/transactions/purchase` | Create purchase |
| POST | `/merchants/transactions/v2/purchase` | Create V2 purchase (async) |
| POST | `/merchants/orders/{order_token}/capture` | Capture authorization |
| POST | `/merchants/orders/{order_token}/v2/capture` | Capture V2 (async) |
| POST | `/merchants/orders/{order_token}/void` | Cancel authorization (void) |
| POST | `/merchants/orders/{order_token}/refund` | Refund payment |
| POST | `/merchants/orders/{order_token}/v2/refund` | Refund V2 (async) |
| POST | `/merchants/transactions/verify-otp` | Verify payment with OTP |
| GET | `/merchants/transactions/stores/{store_code}/payment-methods` | Get merchant payment methods |

## Users

| Method | Endpoint | Description |
|---|---|---|
| POST | `/users/register` | Register user |

## Cards

| Method | Endpoint | Description |
|---|---|---|
| GET | `/users/cards` | Get user cards |

## Addresses

| Method | Endpoint | Description |
|---|---|---|
| GET | `/users/addresses` | Get user addresses |
| POST | `/users/addresses` | Create address |

## Payment links

| Method | Endpoint | Description |
|---|---|---|
| POST | `/merchants/payment-links` | Create payment link |

## Subscriptions

| Method | Endpoint | Description |
|---|---|---|
| GET | `/merchants/subscriptions` | Get subscriptions |
| GET | `/merchants/plans` | Get plans |

## Webhooks

| Method | Endpoint | Description |
|---|---|---|
| POST | `/api/v1/merchants/{merchant_id}/webhooks` | Configure dynamic webhook |

## Common response codes

| Code | Meaning |
|---|---|
| 200 | Success |
| 204 | Success (no content) |
| 400 | Bad request |
| 401 | Unauthorized |
| 404 | Not found |
| 409 | Conflict |
| 422 | Unprocessable entity (processor error) |
| 429 | Rate limit exceeded |
| 500 | Internal server error |

For full endpoint details, parameter definitions, and response schemas, fetch the live docs: https://docs.deuna.com/reference/deuna-api
