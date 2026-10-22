# 0007 · Headless storefront: product pages in the platform, checkout in Shopify

**Status:** `accepted` (21 October 2026)
**Owner:** development
**Deadline:** before P1
**Blocks:** P1 (product detail, cart)
**Builds on:** [0003](0003-landing-and-platform-in-one-deployment.md)

## Context

[0003](0003-landing-and-platform-in-one-deployment.md) moves the landing out of Shopify but still
sends the buyer to a Shopify theme for the product page and cart. That page is where the poster is
judged and the variant chosen, and a theme cannot show the cromo, the layered art or the edition
the way the landing does. [store.md](../domains/store.md) rule 4 asks for the same visual theme in
both places; keeping two front ends in step costs more than having one.

## Options

### A. Shopify theme for product and cart (current specs)

- No extra build. Shopify maintains cart and product page.
- Second front end to theme and keep in step with the landing's tokens.
- The cromo and its effects do not exist there.

### B. Headless: product page and cart in the platform, checkout in Shopify

- The platform renders the product page and the cart from the Storefront API.
- The buyer pays on Shopify's hosted checkout (`cart.checkoutUrl`). Payment, taxes and fraud stay
  with Shopify, which is also the only way to keep its checkout and gateways.
- One front end, one design system, the cromo on the product page.
- More to build: product page, cart, variant selection, and caching of Shopify data.

### C. Fully custom checkout

- Not possible on Basic or Grow. Rejected.

## Proposed decision

B. The platform renders product detail and cart; Shopify keeps catalog, inventory, price, checkout
and the order. Nothing about the commercial order moves out of Shopify.

## Consequences

- New domain: [product-detail.md](../domains/product-detail.md).
- [store.md](../domains/store.md): the "same visual theme" rule now applies only to the checkout,
  which is branded through Shopify's checkout settings (logo, colours, fonts). The Shopify theme is
  not used by buyers; it can stay a password-protected default.
- The Storefront API becomes a P1 dependency for every product view, not only the landing. It needs
  caching (see the domain spec) and a Shopify development store for previews.
- Cart state lives in Shopify (cart ID in a cookie), so it survives across devices only once the
  buyer is identified ([0002](0002-user-identity.md)).
- Buyer-facing emails come from the platform, not from Shopify. See
  [notifications.md](../domains/notifications.md).
