# 03 · Glossary

Shared vocabulary between business and development. If a term appears in a spec, its meaning is the one here.

| Term | Definition |
|---|---|
| **Drop** | Release of one or more posters on a date. The business's unit of time. "The drop of the week." |
| **Season** | Set of drops that form a complete collection. The album grid is a season. |
| **Print run** | Number of units printed of a poster in a drop. Production organizes the queue by drop and by print run. |
| **Poster** | The physical product. Each unit carries a unique printed code. |
| **Code** | Unique identifier printed on each poster. Issued at payment, activated at delivery, redeemed in the account. It is a row in a table. |
| **Redemption** | The buyer's action of tying a code to their account. Requires the code to be active. |
| **Collection** | Set of pieces redeemed by an account, organized by season. |
| **Placeholder** | Empty slot in the season grid for a piece the account does not have yet. It shows that something is coming (the next drop's date, or a "next art" teaser) and links to that drop. |
| **Album** | Phase 3 view of the collection: piece with depth and holographic effect, plus the season grid. |
| **Order** | A paid order in Shopify, replicated in the platform with its state. An order can have several posters and therefore several codes. |
| **State** | Position of the order in its lifecycle. See [orders.md](../domains/orders.md). |
| **Handoff** | Moment when an order passes from the platform to production or back. There are two: platform → production (when it enters the queue) and production → platform (when it ships). |
| **Production panel** | Internal view where production changes states. A table with filters and buttons. |
| **Buyer view** | View inside the account where the buyer sees the state of their order. |
| **Landing** | The system's entry point. Public page with narrative, current drop, and routes to store and account. |
| **Store / storefront** | Shopify: catalog, cart, and checkout. |
| **Platform** | Our code: landing, account, collection, production panel, album. |
| **Gateway** | Provider that processes the payment. Local (Wompi, PayU, ePayco, Mercado Pago) or Shopify Payments / Stripe through a US entity. |
| **Route A / Route B** | The two gateway and jurisdiction options. See [payments.md](../domains/payments.md) and [decision 0001](../decisions/0001-payment-gateway-and-jurisdiction.md). |
| **Order webhook** | `orders/paid` event that Shopify sends to the platform. The only mandatory integration point of P1. |
| **Tracking number** | Carrier's tracking identifier. Production captures it when shipping. |
| **ADR** | Architecture Decision Record. Document that records a decision, its context, and its consequences. They live in [decisions/](../decisions/). |
