# 0001 · Payment gateway and jurisdiction

**Status:** `pending`
**Owner:** _to assign_
**Deadline:** _to assign_
**Blocks:** P1

> Not legal or financial advice. Route B must be validated with an accountant and a lawyer.

## Context

Shopify Payments is not available for Colombian accounts. Using a local gateway means its fee plus a Shopify surcharge. Incorporating a US entity enables Shopify Payments and Stripe, with no surcharge and USD charging, but loses PSE, Nequi, and Daviplata and brings fixed annual costs.

This decision defines fees per sale and who the store can sell to. It should be made before setting up the store, not after. Full detail in [domains/payments.md](../domains/payments.md).

## Options

### A · Local gateway (Colombia)

- Wompi, PayU, ePayco, or Mercado Pago.
- ~5.5% to 6% per sale (gateway + VAT + Shopify surcharge).
- PSE, Nequi, Daviplata. Weak for international sales.
- No additional fixed costs.

### B · US entity

- Delaware LLC via Stripe Atlas (~USD 500) + registered agent + franchise tax + accountant (~USD 900 to 1,400/year).
- ~3.5% to 4.5% per sale.
- International cards, USD, multi-currency with Shopify Markets.
- PSE, Nequi, and Daviplata are lost unless a local gateway is added as a secondary method or two storefronts are opened.

### Sub-decision if B

If both markets are wanted: additional payment methods in the same store, or two storefronts.

## Criteria for deciding

- If the audience is global from drop one, A falls short fast.
- Break-even by cost: USD 5,000 to 6,000 in monthly sales. Below that, A costs less.
- B is not chosen for savings; it is chosen for access.

Data needed: expected split of Colombia vs. international sales in the first three drops.

## Decision

_Pending._

## Consequences

_Completed when decided._ Affects: [store.md](../domains/store.md), [payments.md](../domains/payments.md), [costs.md](../domains/costs.md), [trademark.md](../domains/trademark.md) (owner of the US registration, already decided in [0006](0006-trademark-registration-in-the-us.md): if there is an LLC, the LLC; if not, another owner has to be defined).
