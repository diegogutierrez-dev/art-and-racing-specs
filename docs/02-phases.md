# 02 · Phases

Three phases, two decisions.

## Summary

| Phase | Name | Goal | Deliverables |
|---|---|---|---|
| P1 | Sell | Someone can buy a poster | Landing, store, gateway, order webhook, state emails |
| P2 | Operate | Production works without a spreadsheet | Production panel, full states, account and codes |
| P3 | Collect | The buyer wants the next drop | Digital album and season grid |

## Before P1: two blocking decisions

What has to be decided before writing the first line of code.

| ID | Decision | What it defines | Status |
|---|---|---|---|
| [0001](../decisions/0001-payment-gateway-and-jurisdiction.md) | Payment gateway and jurisdiction: Colombia or US entity | Fees and reach of sales | Pending |
| [0002](../decisions/0002-user-identity.md) | User identity: Shopify accounts or our own auth | Scope of the platform | Pending |

Each decision needs an owner and a date. Without that, phase one does not start.

Also before P1, though it does not block code: [trademark registration](../domains/trademark.md). The jurisdiction is already decided (US, [decision 0006](../decisions/0006-trademark-registration-in-the-us.md)); costs and operations are still to be researched.

## P1 · Sell

**Exit criterion:** a real buyer pays for a poster and receives a confirmation email with their order in state *paid*.

Includes:

- Landing with narrative, current drop, email capture, and route to store.
- Shopify store with catalog and checkout.
- Gateway configured per decision 0001.
- `orders/paid` webhook received by the platform and creating the order.
- Confirmation email and state-change emails (even if the changes are manual in this phase).

Does not include:

- Production panel (in P1, production can receive orders by email or from the Shopify admin).
- Code redemption (codes are **issued** in P1, but redemption arrives in P2).
- Album.

Domains: [landing](../domains/landing.md), [store](../domains/store.md), [payments](../domains/payments.md), [orders](../domains/orders.md) (automatic states).

## P2 · Operate

**Exit criterion:** production updates states from the panel, the buyer sees their order in their account, and redeems the code of their first poster.

Includes:

- Production panel: queue, state change, tracking number and carrier, bulk actions.
- Full states, including exceptions (cancellation, reprint, return).
- Buyer account with order state, history, and tracking.
- Redemption endpoint and basic collection (list of claimed pieces).

Domains: [production](../domains/production.md), [orders](../domains/orders.md) (manual states and exceptions), [account and collection](../domains/account-and-collection.md).

## P3 · Collect

**Exit criterion:** the buyer opens their album on their phone, sees their poster with depth and holographic effect, and sees the season grid with placeholders for the missing pieces.

Includes:

- Digital album: layers, holographic shader, device orientation.
- Season grid with placeholders for the missing pieces.

Does not block launch. Codes are issued from drop one; the album can arrive at drop six without losing anything.

Domain: [album](../domains/album.md).

## Dependencies between phases

```
Decision 0001 ──┐
                ├──▶ P1 Sell ──▶ P2 Operate ──▶ P3 Collect
Decision 0002 ──┘       │
                        └── codes issued from here
```

- P2 depends on P1 because the panel operates on real orders.
- P3 depends on P2 because the album shows redeemed pieces.
- Decision 0002 affects P1 (identity at checkout) and P2 (account), which is why it blocks even though the account arrives in P2.
