# 0002 · User identity

**Status:** `pending`
**Owner:** _to assign_
**Deadline:** _to assign_
**Blocks:** P1

## Context

The buyer needs an account to see their orders, redeem codes, and see their collection. That identity can come from Shopify (customer accounts) or be the platform's own. It defines the scope of everything else and cannot be changed cheaply later. Detail in [domains/account-and-collection.md](../domains/account-and-collection.md).

## Options

### A · Shopify customer accounts

- Shopify is the only source of identity. The platform uses the Customer Account API to authenticate and read the customer.
- The order already comes tied to the customer from the webhook. No email reconciliation needed.
- Phase one gets weeks shorter.
- Strong dependency on Shopify: if the store is ever changed, identity has to be migrated.
- Less control over the sign-in flow (Shopify's login, with its UX).

### B · Our own auth

- The platform has its own identity (email + magic link, or an auth provider).
- Full control over flow, design, and data.
- Two sources of identity have to be synchronized: the Shopify customer (from the webhook) and the platform account. Reconciliation is by email, with its edge cases (different email at checkout, casing, aliases).
- More weeks in P1 and P2.

## Criteria for deciding

- If the priority is shipping P1 and P2 fast, A.
- If the priority is the platform being independent from Shopify long term, B.
- A middle path (A now, B later) is possible but costs an identity migration. That cost has to be accepted consciously.

## Decision

_Pending._

## Consequences

_Completed when decided._ Affects: [landing.md](../domains/landing.md) (account access), [store.md](../domains/store.md) (shared session), [account-and-collection.md](../domains/account-and-collection.md), [tech/stack.md](../tech/stack.md) (auth provider).
