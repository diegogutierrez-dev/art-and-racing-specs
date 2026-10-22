# 0009 · The cromo follows the order, without a code

**Status:** `proposed` (21 October 2026). "No codes" is decided; the moments below are the recommendation
**Owner:** development
**Supersedes:** the code mechanism in [orders.md](../domains/orders.md) (states 06 and 07),
[account-and-collection.md](../domains/account-and-collection.md) and [album.md](../domains/album.md)

## Context

The specs give every printed poster a code on the back. The buyer redeems it in their account and the
poster's cromo enters the album. The team decided not to use codes: printing and tracking a unique code
per copy adds work to production, and the buyer already identifies themselves when they pay.

Without a code, the platform has to decide on its own when a cromo belongs to someone, and what happens
when the order is cancelled or returned.

## Options

- **Assign on delivery.** The cromo appears only when the poster arrives. Delivery is the hardest event
  to know: it depends on a carrier integration or on production marking it by hand. The buyer waits days
  with nothing in the album.
- **Assign on payment, remove on cancel.** Immediate, but someone could buy six posters, unlock the
  Champion Edition and cancel.
- **Assign on payment as a silhouette, complete on shipping, remove on cancel or return.** The buyer sees
  their cromo straight away; it only counts once the poster has left the studio, an event production
  already marks with the tracking number.

## Proposed decision

The third option.

| Order event | Cromo in the album | Counts toward the Champion Edition |
|---|---|---|
| 01 Paid | Appears as a silhouette | No |
| 05 Shipped | Fills in | Yes |
| Cancelled | Removed | — |
| Return / refund | Removed | Stops counting |

- The cromo belongs to the email on the order. Nothing is redeemed.
- The Champion Edition unlocks when the buyer's **shipped** posters of the season reach the unlock count.
  Once it has shipped it is not taken back if a poster is returned later.
- A reprint keeps the cromo as it is.

## Consequences

- No codes are printed, generated or stored. States *06 Delivered* and *07 Collection active* no longer
  activate anything; 06 stays only to send *¿Llegó bien?* and close the order.
- A poster given as a gift or resold leaves its cromo with the buyer. Transferring a cromo ("give this
  cromo") is a later feature, from the account.
- Emails: T1 says the cromo is already in the album as a silhouette; T3 says it is complete; T5 and T6 say
  it was removed; T4 becomes a delivery check; T7 and M4 count shipped posters. See
  [notifications.md](../domains/notifications.md).
- To rewrite when this is accepted: [account-and-collection.md](../domains/account-and-collection.md),
  [album.md](../domains/album.md), [product-detail.md](../domains/product-detail.md),
  [landing.md](../domains/landing.md), [store.md](../domains/store.md),
  [production.md](../domains/production.md), the README and the MANIFESTO, which all describe codes.
- The site copy that mentions codes (header "Canjear código", how-it-works, essentials, product facts, the
  code chip in the cromo anatomy) changes with it.
