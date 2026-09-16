# Domain · Store

**Phase:** P1
**Piece:** Level 2, Shopify
**Guiding principle:** Shopify is catalog and checkout, not the platform

## Purpose

Where the transaction happens. Shopify handles catalog, cart, checkout, taxes, and the order admin. None of that gets replicated.

## Responsibilities

- Product catalog (one product per poster, variants if there are sizes or editions).
- Cart and checkout.
- Charging through the gateway defined in [decision 0001](../decisions/0001-payment-gateway-and-jurisdiction.md).
- Multi-currency and regional pricing (Shopify Markets) if route B is taken.
- Firing the `orders/paid` webhook to the platform.
- Source of truth for inventory and price.

## Not the store's responsibility

- Production states. Shopify has its own concept of *fulfillment*, but the state lifecycle lives in the platform. See [orders.md](orders.md).
- Codes or collection. Shopify does not know they exist.
- Narrative. That is the landing's job.

## Rules

1. **The order webhook is mandatory.** It is the only integration point of P1. Without it, the platform does not know a sale happened.
2. **Shopify is the source of truth for the commercial order.** Amount, items, address, customer. The platform replicates what it needs and stores the Shopify ID as a reference.
3. **The platform is the source of truth for the operational state.** Shopify is not updated with production states unless it is decided to sync fulfillment on shipping (open question).
4. **The store's visual theme is the same as the landing's.** The buyer should not feel like they switched products.

## Integrations

| Integration | Direction | Use | Phase |
|---|---|---|---|
| `orders/paid` webhook | Shopify → Platform | Create order and issue codes | P1 |
| Storefront API | Platform → Shopify | Show the drop's product on the landing | P1 |
| `orders/cancelled` webhook | Shopify → Platform | Mark order as cancelled | P2 |
| `refunds/create` webhook | Shopify → Platform | Record a return | P2 |
| Admin API (fulfillment) | Platform → Shopify | Sync tracking number on shipping | P2, optional |
| Customer Account API | Platform ↔ Shopify | Identity if Shopify accounts are chosen | Depends on 0002 |

## Shopify plan

Basic (39 USD/month, 29 with annual billing) is enough for P1. The external gateway surcharge depends on the plan: 2% Basic, 1% Grow, 0.6% Advanced. See [payments.md](payments.md) and [costs.md](costs.md).

## Acceptance criteria (P1)

- [ ] A buyer completes a test payment and the platform receives the webhook with the order data.
- [ ] The webhook is idempotent: receiving the same event twice does not create two orders.
- [ ] The webhook's HMAC signature is verified before processing.
- [ ] The current drop's product is shown on the landing with real price and availability.

## Open questions

- Sync Shopify fulfillment when production marks *shipped*? Gives consistency in the Shopify admin and native emails, but duplicates a source of state. Proposal: yes, but only as an outgoing mirror, never as input.
- One store or two? If the goal is to charge in Colombia with PSE/Nequi and abroad with cards, it may require two storefronts. See [payments.md](payments.md).
- Which Shopify apps are needed? Budget of 0 to 100 USD/month depending on what is missing.
