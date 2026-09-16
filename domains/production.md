# Domain · Production (panel)

**Phase:** P2
**Piece:** Level 3, platform
**Guiding principle:** deliberately boring

## Purpose

The internal view where the production team marks states 03, 04, and 05 of the [order lifecycle](orders.md). A table, filters, and state buttons. No dashboards.

## Who uses it

The production team. It is the only interface production needs; if they need something more, it gets added here.

## Responsibilities

| Function | Detail |
|---|---|
| Order queue | Filterable by drop and by print run. Sorted by time in the current state |
| State change | One click per valid transition. The button only shows transitions allowed from the current state |
| Tracking number and carrier | Mandatory fields when marking *shipped* |
| Bulk actions | Select several orders and apply the same transition. For large shipments |
| Exceptions | Mark cancellation, reprint, or return with a mandatory note |
| History | See an order's state events, with author and date |

## What the panel does NOT have

- **Payment data.** No card, no account, no detailed method. Only the total amount, as text.
- **Dashboards or charts.** If production needs a report, the table gets exported.
- **Editing the commercial order.** Items, address, and amount are edited in Shopify, not here.
- **Code management.** Codes are visible tied to the order, but they are not edited from the panel.

## Main view

A table. Minimum columns:

| Column | Note |
|---|---|
| Order number | Link to detail |
| Drop | Filter |
| Items | Summary: "2× Poster A, 1× Poster B" |
| Current state | With time in that state |
| Buyer | Name and city. No full email unless needed |
| Actions | Valid transition buttons |

Filters: drop, print run, state, date range. Search by order number or name.

## Rules

1. **Only valid transitions.** The panel does not allow skipping states. See the table in [orders.md](orders.md).
2. **Tracking number mandatory on shipping.** Without tracking number and carrier, 05 cannot be marked.
3. **Note mandatory on exceptions.** Cancelling, reprinting, or returning requires a written reason.
4. **Every change has an author.** Each production user has their own account. No shared user.
5. **Bulk actions are confirmed.** Shows how many orders will move and to which state before applying.
6. **One record.** The panel writes to the same record the [buyer view](account-and-collection.md) reads. There is no copy.

## Access

- Authentication separate from the buyer's. A production user is not a customer account.
- Minimum roles: `production` (changes states) and `admin` (also manages production users and sees costs).
- No public access. Protected route.

## Acceptance criteria (P2)

- [ ] A production user enters the panel and sees the queue filtered by the current drop.
- [ ] Moves an order from 02 to 03 with one click and the buyer sees the change in their account.
- [ ] Tries to mark 05 without a tracking number and the system prevents it.
- [ ] Selects ten orders and marks them as shipped in bulk, each with its tracking number.
- [ ] Records a reprint with a note and it appears in the order's history.
- [ ] Sees no payment data in any view.

## Open questions

- Does production need to print labels or packing lists from the panel? If so, it is a P2 function that needs sizing.
- Bulk upload of tracking numbers from a carrier CSV? Useful in large shipments. Proposal: not in P2, evaluate in P3.
- Who in production validates the panel design before it is built? See [00-scope.md](../docs/00-scope.md).
