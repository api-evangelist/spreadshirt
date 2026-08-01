---
name: Create and fulfill a print-on-demand order
description: Create an order on SpreadConnect (SPOD), choose a shipping type, confirm it for production, and track the shipment.
api: openapi/spreadshirt-spreadconnect-openapi.json
operations:
  - authentication info
  - createOrder
  - getShippingTypes
  - setShippingType
  - confirmOrder
  - getShipments
---

# Create and fulfill a print-on-demand order (SpreadConnect / SPOD)

Use the SpreadConnect fulfillment API to place an on-demand order and track it to shipment.

## Auth
Send `X-SPOD-ACCESS-TOKEN: <your token>` on every request. Generate the token in the
Spreadconnect web app (Connect Integration). Verify it with `authentication info`
(`GET /authentication`) before doing real work.

## Steps
1. **Create the order** — `createOrder` (`POST /orders`) with the customer address and line
   items (each item references a product/variant SKU and design). On success you get a
   `GetOrder` with an `orderId`. On a `422` the body is an `ErrorResponse {orderId, reason}`.
2. **List shipping options** — `getShippingTypes` (`GET /orders/{orderId}/shippingTypes`).
3. **Set the shipping type** — `setShippingType` (`POST /orders/{orderId}/shippingType`).
4. **Confirm for production** — `confirmOrder` (`POST /orders/{orderId}/confirm`). Until an
   order is confirmed it is not sent to fulfillment.
5. **Track shipment** — `getShipments` (`GET /orders/{orderId}/shipments`) for tracking data,
   or subscribe to the `Shipment.sent` webhook instead of polling.

## Rules
- Rate limit: 60 requests/minute per integration. Honor `X-RateLimit-Remaining` and back off
  by `X-RateLimit-Retry-After-Seconds` on `429`.
- No idempotency key is supported — do not blindly retry `createOrder`; check for an existing
  order first to avoid duplicates.
- Test end-to-end against the staging host `https://rest.spreadconnect-staging.app` and drive
  events with the `simulate/*` endpoints before going live.
