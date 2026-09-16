# Domain · Orders

**Phase:** P1 (automatic states) → P2 (manual states and exceptions)
**Piece:** Level 3, platform
**Guiding principle:** one record of state

## Purpose

Model the lifecycle of an order from payment until the collection becomes active. One record of state feeds both views (production and buyer). Nothing gets updated twice and there is no parallel spreadsheet.

## State lifecycle

```
 PLATFORM · AUTO           PRODUCTION · MANUAL              PLATFORM · AUTO
┌─────────┐ ┌─────────┐   ┌──────────────┐ ┌──────────┐ ┌────────────┐   ┌───────────┐ ┌──────────────────┐
│01 Paid  │▶│02 Queued│──▶│03 In         │▶│04 Packed │▶│05 Shipped  │──▶│06         │▶│07 Collection     │
│         │ │         │   │   production │ │          │ │            │   │ Delivered │ │   active         │
└─────────┘ └─────────┘   └──────────────┘ └──────────┘ └────────────┘   └───────────┘ └──────────────────┘
                       handoff                                        handoff
```

| # | State | Who marks it | Trigger | Effect |
|---|---|---|---|---|
| 01 | **Paid** | Platform | Shopify `orders/paid` webhook | Order is created, codes are issued (inactive), confirmation email |
| 02 | **Queued** | Platform | Automatic after 01 | Ready to produce. Appears in the production panel |
| 03 | **In production** | Production | Click in the panel | The team marks the start |
| 04 | **Packed** | Production | Click in the panel | Ready to ship |
| 05 | **Shipped** | Production | Click in the panel + tracking number + carrier | Email with tracking number and tracking link |
| 06 | **Delivered** | Platform | Automatic (tracking) or manual if there is no integration | Closes the order. Codes become active |
| 07 | **Collection active** | Platform | Automatic after 06 | The code is redeemable in the account |

### The two handoffs

- **02 → 03:** the platform hands off to production. The platform provides the panel where production marks 03, 04, and 05. None of those three happen in code.
- **05 → 06:** production hands back to the platform. From shipping onward, the platform is again the one informing the buyer.

## Exceptions

Exceptions are modeled from day one. Each one is a state, not a special case.

| State | From | Who marks it | Effect |
|---|---|---|---|
| **Cancelled** | 01, 02 (before producing) | Platform via `orders/cancelled` webhook, or production from the panel | Codes voided. Refund is handled by Shopify |
| **Reprint** | 03, 04, 05, 06 | Production | Goes back to 03 with a reprint flag. Original codes are kept (the new poster carries the same code) or reissued (production decides case by case) |
| **Return** | 06 | Production via Shopify `refunds/create` or manually | Codes voided if they were already active. The poster leaves the collection |

Exception rules:

1. An order in an exception keeps its history. A state is never deleted, one is added.
2. Cancelling an order with already-redeemed codes is not possible from the panel. It requires a return.
3. A reprint does not change the state visible to the buyer unless it is decided to communicate it (open question).

## Entities

### Order

| Field | Type | Source | Note |
|---|---|---|---|
| id | identifier | Platform | |
| shopify_order_id | reference | Shopify | Unique. Basis of webhook idempotency |
| number | text | Shopify | The number visible to the buyer (#1001) |
| buyer | reference to account or email | Shopify | Depends on [decision 0002](../decisions/0002-user-identity.md) |
| items | list | Shopify | Product, variant, quantity. Each unit generates a code |
| amount | number + currency | Shopify | No conversion |
| current_state | enum | Platform | Derived from the last state event |
| drop | reference | Platform | For the queue by drop |
| tracking_number | text | Production | On shipping |
| carrier | text or enum | Production | On shipping |
| created_at | date | Platform | |

### State event

The state is not overwritten. One event is recorded per change and `current_state` is the latest.

| Field | Type | Note |
|---|---|---|
| order_id | reference | |
| state | enum | One of the states above |
| marked_by | reference to user or `system` | Audit |
| note | optional text | Exception reason, tracking number, etc. |
| marked_at | date | |

## Notifications

Automatic email on every state change visible to the buyer.

| State | Email | Minimum content |
|---|---|---|
| 01 Paid | Confirmation | Order number, items, link to account |
| 03 In production | Optional | "Your poster is in production" |
| 05 Shipped | Yes | Tracking number, carrier, tracking link |
| 06 Delivered | Yes | "You can now claim your code" with redemption link |
| Cancelled | Yes | Cancellation confirmation |

States 02 and 04 are internal and generate no email.

## Rules

1. **Idempotency.** Receiving the same webhook twice creates neither two orders nor two events.
2. **One event per change.** The history is the source of truth. `current_state` is a projection.
3. **Valid transitions.** The system rejects transitions not in the table (for example, from 01 to 05).
4. **Emails fire on events**, not on polling. If the email fails, the state is not reverted; the email is retried.
5. **The panel does not see payment data.**

## Acceptance criteria

P1:
- [ ] A payment in Shopify creates an order in 01 and moves it to 02 automatically.
- [ ] The buyer receives a confirmation email.
- [ ] The same webhook received twice duplicates nothing.

P2:
- [ ] Production moves an order through 03, 04, and 05 from the panel.
- [ ] On shipping, the buyer receives tracking number and tracking link.
- [ ] On delivery, the order's codes become active.
- [ ] Cancellation, reprint, and return are recorded with their note and author.

## Open questions

- How is *delivered* detected? Carrier integration, manual marking by production, or buyer confirmation. Proposal for P2: manual by production, with integration later.
- Is a reprint communicated to the buyer? Business decides.
- What happens with an order that has been in 05 for N days without reaching 06? May require an *incident* state or an alert in the panel.
