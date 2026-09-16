# 00 · Scope

What happens inside the platform and what happens outside it.

## Inside the platform

| Piece | What it includes |
|---|---|
| Landing page | Entry to store and platform. Narrative, drops, email capture |
| Storefront and checkout | Catalog, cart, and payment on Shopify |
| Payment gateway and internationalization | Gateway integration, currencies, regional pricing |
| Accounts, codes, and collection | Buyer identity, code issuance and redemption, collection by season |
| Production state panel | Interface where the production team updates the state of each order |

## Outside the platform

| Area | Where it happens |
|---|---|
| Art creation and curation | Creative team |
| Printing, packaging, and physical logistics | Production |
| Product quality and reprints | Production |
| Relationship with printers and carriers | Production |
| Customer service on the physical order | Production / operations |

## The line

**The platform stops at the order.** When an order comes in paid, it hands off to production. From then on, production executes and updates states in the platform.

This means:

- The platform provides the panel where production marks *in production*, *packed*, and *shipped*. None of those three happen in code.
- If an order gets lost at the carrier, the platform must allow recording it (see [orders.md](../domains/orders.md), exceptions), but resolving it is a production task.
- If a print comes out wrong, the platform must allow marking a reprint. Deciding whether to reprint is a production task.

## Why it is written down

This line exists so that everyone on the team knows which problems are solved with code and which are solved on the production floor. A lost order goes to production. A wrong state in the panel goes to development.

## Open questions

- Who is the contact person in production who validates the panel? Without that person, the panel gets designed blind.
- Will customer service on the physical order have access to the production panel, or read-only access from another view? See [production.md](../domains/production.md).
