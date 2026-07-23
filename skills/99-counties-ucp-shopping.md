---
name: Shop 99 Counties via UCP
description: Search the 99 Counties regenerative-meat catalog and complete a buyer-approved purchase over the Universal Commerce Protocol (UCP) MCP endpoint.
api: mcp/99-counties-mcp.yml
method: generated
source: https://99counties.com/llms.txt
operations:
- search_catalog
- create_cart
- create_checkout
- update_checkout
- complete_checkout
---

# Shop 99 Counties via UCP

99 Counties is a Shopify storefront (regenerative beef, bison, pork, poultry, lamb, seafood)
that exposes agent-driven commerce through the Universal Commerce Protocol (UCP) over MCP.

## Endpoints
- Discovery: `GET https://99counties.com/.well-known/ucp`
- MCP: `POST https://99counties.com/api/ucp/mcp` (`Content-Type: application/json`, JSON-RPC 2.0)
- Read-only catalog: `GET /products/{handle}.json`, `GET /collections/{handle}/products.json`

## Steps
1. **Discover** — `GET /.well-known/ucp` to confirm supported versions (`2026-04-08` latest) and capabilities.
2. **Search** — call `search_catalog` with the buyer's intent. Pass `context.address_country` and `context.currency` for accurate pricing/availability.
3. **Cart** — call `create_cart` to add the chosen product variants.
4. **Checkout** — call `create_checkout` to open the purchase flow.
5. **Fulfill** — call `update_checkout` to set the shipping address and method.
6. **Complete** — call `complete_checkout` ONLY after obtaining explicit, contemporaneous buyer approval of the payment.

## Rules
- **Buyer approval is mandatory** before `complete_checkout`. If you cannot get approval at the moment of payment, do not complete — route through the Shop skill (`https://shop.app/SKILL.md`) and Shop Pay instead.
- **Respect rate limits** — the MCP endpoint is rate-limited per IP; back off on HTTP 429.
- **`tools/list` needs a UCP agent profile URI** — discovery fails (`invalid_profile_url`) without one.
- Authenticated customer-account operations use Shopify Customer Account OAuth 2.0 / OIDC (PKCE S256); see `authentication/99-counties-authentication.yml`.
