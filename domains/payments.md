# Domain · Payments

**Phase:** P1
**Piece:** Level 2, inside the store
**Guiding principle:** the most expensive thing to get wrong

> **Notice.** This document is not legal, accounting, or financial advice. Route B means incorporating an entity in another country and must be validated with an accountant and a lawyer before executing it. The Colombian side of receiving income from abroad, too.

## The problem

Shopify Payments is not available for Colombian accounts. On top of the local gateway's fee, Shopify charges a surcharge for using an external gateway: 2% on Basic, 1% on Grow, 0.6% on Advanced.

If the audience is global from drop one, route A falls short fast. It is better to decide this before setting up the store, not after.

## Route A · Local gateway

| Aspect | Detail |
|---|---|
| Providers | Wompi, PayU, ePayco, or Mercado Pago |
| Fee | ~2.65% to 3.99% + VAT per transaction |
| Shopify surcharge | Yes, on top of the fee |
| Strength | Excellent for Colombia: PSE, Nequi, Daviplata |
| Weakness | Weak for international sales |
| Total cost per sale | ~5.5% to 6% |

## Route B · US entity

| Aspect | Detail |
|---|---|
| Enables | Shopify Payments and Stripe |
| Fee | ~2.9% + 30¢ |
| Shopify surcharge | No |
| Strength | Native USD charging and multi-currency, international cards |
| Weakness | Requires incorporation, bank, and accounting in the US. PSE, Nequi, and Daviplata are lost |
| Total cost per sale | ~3.5% to 4.5% |

### How route B is set up

| Step | What | Note |
|---|---|---|
| 01 | Delaware LLC | Stripe Atlas, ~USD 500 once: filing, EIN, year-one agent, and bank account |
| 02 | LLC, not C-corp | A C-corp means double taxation and more expensive accounting |
| 03 | US bank account | Included in the package, no travel |
| 04 | Activate Shopify Payments | With the EIN. The external gateway surcharge disappears |
| 05 | Shopify Markets | Multi-currency and regional pricing |
| 06 | US accountant | Federal filings are mandatory even with no profit |

### Cost of the structure

| Item | Cost |
|---|---|
| Setup, one time | ~USD 500 |
| Registered agent | USD 100/year |
| Delaware franchise tax | ~USD 300/year |
| US accountant | USD 500 to 1,000/year |

### Break-even

Around USD 5,000 to 6,000 in monthly sales. Below that, route A costs less. But B is not chosen for savings: it is chosen for access to international cards and USD charging.

## The important warning

With Shopify Payments, PSE, Nequi, and Daviplata are lost, and that is how most people in Colombia pay. If both markets are wanted, there is a choice between:

1. **Additional payment methods** in the same store (local gateway as a secondary method, with its surcharge).
2. **Two storefronts**, one per market, with the operational cost that implies.

This is a sub-decision of [decision 0001](../decisions/0001-payment-gateway-and-jurisdiction.md).

## Rules for the platform

Regardless of the route:

1. **The platform never touches payment data.** It does not store cards, does not process charges, does not see account numbers. All of that lives in Shopify and the gateway.
2. **The production panel has no access to payment data.** It sees the total amount and the method as text.
3. **The amount is stored in the transaction's currency**, with the currency explicit. It is not converted.

## Acceptance criteria (P1)

- [ ] Decision 0001 is made, with owner and date.
- [ ] The gateway processes a real end-to-end test payment.
- [ ] Real fees are quoted in writing with the chosen provider.

## Open questions

- What is the expected split of Colombia vs. international sales in the first three drops? That is the number that decides the route.
- Are there an accountant and a lawyer identified to validate route B?

## Figure verification

Prices and fees verified in September 2026. Gateway percentages vary by negotiation and volume; request a direct quote before setting the sale price.
