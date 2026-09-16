# Domain · Account and collection

**Phase:** P2 (codes are issued from P1)
**Piece:** Level 3, platform
**Guiding principle:** minimal backend

## Purpose

From delivery to collection. The buyer receives a poster with a unique printed code, claims it in their account, and the piece appears in their season collection, next to placeholders for the pieces they do not have yet.

```
┌────────────┐   ┌────────────┐   ┌────────────┐   ┌────────────┐
│   Poster   │──▶│ Redemption │──▶│  Account   │──▶│ Collection │
│ printed    │   │ scan and   │   │ identity   │   │ season,    │
│ code       │   │ claim      │   │ and history│   │placeholders│
└────────────┘   └────────────┘   └────────────┘   └────────────┘
```

## Minimal backend

Three pieces:

1. **Codes table.** One code per unit sold.
2. **Redemption endpoint.** Receives a code and an account, validates, and ties them.
3. **Order states table.** Already defined in [orders.md](orders.md).

No blockchain, no native app, no heavy infrastructure.

## Sub-domains

### Account

The buyer's identity and history.

| Function | Detail |
|---|---|
| Sign in | Per [decision 0002](../decisions/0002-user-identity.md): Shopify accounts or our own auth |
| Order state | Each order with its current state, tracking number, and tracking link |
| History | All their drops, orders, and pieces |
| Redeem code | Form or scan (QR) |
| Collection | Season view |

The account is the **buyer view** over the same state record that production uses. See [orders.md](orders.md).

### Code

| Field | Type | Note |
|---|---|---|
| code | unique text | Printed on the poster. Readable and not guessable format |
| order_id | reference | The order that generated it |
| item | reference | Which poster, which variant |
| drop | reference | To place it in the season |
| state | enum | `issued`, `active`, `redeemed`, `voided` |
| account_id | optional reference | Filled on redemption |
| issued_at / activated_at / redeemed_at | dates | |

Code lifecycle:

```
issued ──(order delivered)──▶ active ──(redemption)──▶ redeemed
   │                             │
   └──────(cancellation)─────────┴──(return)──▶ voided
```

- **Issued:** created when the payment webhook is received (P1). Cannot be redeemed yet.
- **Active:** the order reached *delivered*. Redeemable.
- **Redeemed:** tied to an account. Appears in the collection.
- **Voided:** cancellation or return. Not redeemable; if it was redeemed, it leaves the collection.

### Redemption

Single endpoint. Input: code + authenticated account. Output: piece in the collection or error.

Rules:

1. Only codes in state `active`.
2. A code is redeemed once. A second attempt with the same code returns a clear error ("already claimed").
3. A code can be redeemed in an account other than the original buyer's (gift, resale). The code belongs to whoever has the poster.
4. Attempt limit per account and per IP to prevent brute force.
5. Redemption records date and account. It cannot be undone from the account; only through a return.

### Collection

The season, with placeholders for what is missing.

| Function | Detail |
|---|---|
| Season grid | All positions in the season. Redeemed ones show the piece; the rest are placeholders that announce the next drop |
| Piece detail | Image, illustrator, drop, redemption date |
| Multiple seasons | One grid per season, current one by default |

In P2 the collection is a simple list or grid. In P3 it becomes the [album](album.md).

## Domain rules

1. **Codes are issued from drop one.** Even if redemption arrives in P2, every poster sold in P1 already has its code in the table and that code is printed.
2. **The code is a row in a table.** It is not transferred, not sold, and has no value outside the collection.
3. **The collection is not edited by hand.** It is only filled by redemption and emptied by return.
4. **No payment data in the account.** The buyer sees amount and method as text. For invoices, they go to Shopify.

## Printing the code

The code is printed on the poster. That is production's job, but the platform must:

- Expose the codes of an order (or a print run) in an exportable format for the printer.
- Generate the corresponding QR if scanning is chosen (the QR points to the redemption URL with the code).

## Blocking decision

[0002 · User identity](../decisions/0002-user-identity.md). Shopify customer accounts as identity, or our own auth? It defines the scope of everything else and cannot be changed cheaply later. With Shopify accounts, phase one gets weeks shorter. With our own auth, two sources of identity have to be synchronized.

## Acceptance criteria (P2)

- [ ] On payment, each unit in the order has an `issued` code in the table.
- [ ] On marking *delivered*, the order's codes become `active`.
- [ ] The buyer signs in to their account, redeems the code, and sees the piece in their collection.
- [ ] A second redemption of the same code fails with a clear message.
- [ ] A return voids the code and removes it from the collection.
- [ ] Production can export a print run's codes for the printer.

## Open questions

- Code format? Proposal: 8 to 10 unambiguous alphanumeric characters (no 0/O, 1/l/I), with a season prefix.
- QR in addition to the readable code? Raises redemption conversion, but requires a stable URL from the first print run.
- What happens if someone buys the same poster twice? Two codes, one position in the grid. Show "×2"?
