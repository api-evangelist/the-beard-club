---
name: Shop and checkout at The Beard Club (UCP)
description: Discover products, build a cart, and complete a buyer-approved purchase at The Beard Club using its Shopify Universal Commerce Protocol (UCP) MCP endpoint.
api: mcp/the-beard-club-mcp.yml
protocol: UCP 2026-04-08
endpoint: https://thebeardclub.com/api/ucp/mcp
operations:
- search_catalog
- create_cart
- create_checkout
- update_checkout
- complete_checkout
source: https://thebeardclub.com/llms.txt
method: generated
---

# Shop and checkout at The Beard Club

The Beard Club (a men's grooming and beard-care store on Shopify) exposes agent
commerce through the Universal Commerce Protocol (UCP). Use the MCP endpoint at
`POST https://thebeardclub.com/api/ucp/mcp` (`Content-Type: application/json`,
JSON-RPC 2.0). All tool names below are published in the store's `/llms.txt`;
their full input/output schemas are at
`https://ucp.dev/2026-04-08/services/shopping/mcp.openrpc.json`.

## Before you start
- **Discover** the store's capabilities: `GET https://thebeardclub.com/.well-known/ucp`.
  Confirm the `dev.ucp.shopping` service and protocol version (`2026-04-08`).
- The MCP `tools/list` call requires UCP **agent-profile discovery** — present your
  agent profile URI so the endpoint can resolve your identity.
- Pass buyer context on requests: `context.address_country` and `context.currency`
  for accurate pricing and availability.

## Steps
1. **Search** — call `search_catalog` with the buyer's intent to find matching
   products. For quick read-only lookups you can also `GET /products/{handle}.json`
   or `GET /collections/{handle}/products.json` (no auth required).
2. **Cart** — call `create_cart` to add the chosen items.
3. **Checkout** — call `create_checkout` to open the purchase flow for the cart.
4. **Fulfill** — call `update_checkout` to set the shipping address and fulfillment
   method. Note the store ships to a single destination per order.
5. **Complete** — call `complete_checkout` to finalize.

## Rules you must honor
- **Buyer approval is mandatory.** Never call `complete_checkout` without explicit,
  contemporaneous buyer consent at the moment of payment. If you cannot obtain it,
  route the purchase through Shop Pay via `https://shop.app/SKILL.md` instead.
- **Back off on rate limits.** The MCP endpoint is rate-limited per IP; retry with
  backoff on HTTP `429`.
- **No idempotency key is documented** — do not assume safe automatic retries of
  cart/checkout mutations; re-discover state before retrying.

See also: `conventions/the-beard-club-conventions.yml`,
`authentication/the-beard-club-authentication.yml`.
