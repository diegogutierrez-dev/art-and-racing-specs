# 01 · System

The three pieces and how they connect.

## The three levels

The system is made of three pieces that the buyer goes through in order. Each one is a deeper level of commitment to the brand.

```
┌──────────────┐     ┌──────────────┐     ┌──────────────────┐
│   LANDING    │ ──▶ │    STORE     │ ──▶ │     PLATFORM     │
│ entry point  │     │   Shopify    │     │    our code      │
└──────────────┘     └──────────────┘     └──────────────────┘
   Tell                 Buy                  Collect
   Sell                 Pay                  Operate
   Retain               Order
```

### Level 1 · Landing

The entry point.

- Narrative and drops
- Email capture
- Routes to store and to account

Spec: [domains/landing.md](../domains/landing.md)

### Level 2 · Store (Shopify)

Where the transaction happens. Shopify is used as catalog and checkout, not as the platform.

- Catalog and checkout
- Gateway and currencies
- Order webhook

Specs: [domains/store.md](../domains/store.md), [domains/payments.md](../domains/payments.md)

### Level 3 · Platform

Our code. Where everything Shopify does not do lives.

- Account and collection
- Order states
- Production panel
- Album (phase 3)

Specs: [domains/orders.md](../domains/orders.md), [domains/production.md](../domains/production.md), [domains/account-and-collection.md](../domains/account-and-collection.md), [domains/album.md](../domains/album.md)

## How they connect

### A single session

The buyer goes through the three pieces in a single session and should never feel like they switched products. This requires:

- Same root domain (the store can live on a subdomain, but with the same visual theme).
- Consistent identity: if the buyer signed in on the platform, the store must recognize them, and vice versa. How that is achieved depends on [decision 0002](../decisions/0002-user-identity.md).
- Cross navigation: from any piece, the other two are one click away.

### The only mandatory integration point

The Shopify order webhook is the only mandatory integration point of phase one. Everything else can come later.

```
Shopify ──(orders/paid)──▶ Platform
                              │
                              ├─ creates the order in state "paid"
                              ├─ generates the associated codes
                              └─ sends the confirmation email
```

See [domains/orders.md](../domains/orders.md) for the initial state and [domains/account-and-collection.md](../domains/account-and-collection.md) for code issuance.

### Deployments

Proposed decision: landing and platform in the same deployment, outside Shopify. Shopify only as catalog and checkout. See [decision 0003](../decisions/0003-landing-and-platform-in-one-deployment.md).

That gives two deployments in total:

1. **Shopify** (store): theme, catalog, checkout.
2. **Platform** (our code): landing, account, collection, production panel, album.

## Full buyer flow

1. Arrives at the landing (organic, social, email).
2. Sees the drop of the week. Clicks the product.
3. Lands in the store. Adds to cart. Pays.
4. Shopify fires the webhook. The platform creates the order and the codes.
5. Receives a confirmation email with a link to their account.
6. Production moves the order through its states. Every change reaches them by email.
7. Receives the poster with the printed code.
8. Scans or enters the code in their account. The poster appears in their collection.
9. Sees the season grid, with placeholders for the pieces they do not have yet.
10. Comes back to the landing for the next drop.

## Open questions

- The "three levels" of the platform can also be described from the business side (for example, buyer access or membership levels). That description is still pending and may need its own section. Until then, this document uses the three pieces of the system as the three levels.
