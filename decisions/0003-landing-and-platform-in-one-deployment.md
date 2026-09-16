# 0003 · Landing and platform in one deployment, outside Shopify

**Status:** `proposed`
**Owner:** development
**Deadline:** before P1
**Blocks:** P1

## Context

The landing can live inside Shopify (as a theme page) or separately, in the same deployment as the platform. Detail in [domains/landing.md](../domains/landing.md).

## Options

### Inside Shopify

- Faster and no extra cost.
- Design is limited by the theme.
- The platform lives separately anyway, so there are two deployments regardless: Shopify (store + landing) and platform.

### Separately, with the platform

- Full control over design.
- It is where the platform lives: same session, same code, same visual identity.
- Two deployments to maintain: Shopify (store only) and platform (landing + account + panel + album).

## Proposed decision

Landing and platform in the same deployment, outside Shopify. Shopify only as catalog and checkout.

It is debatable depending on how fast the launch needs to be. If P1 has a very short date, the landing inside Shopify is acceptable as a bridge, on the condition of migrating it in P2.

## Consequences

- Two deployments: Shopify and platform. See [docs/01-system.md](../docs/01-system.md).
- The drop's product is shown on the landing via Storefront API.
- The landing can know whether the buyer has a session on the platform.
- The platform stack (Next.js on Vercel) also serves the landing. See [tech/stack.md](../tech/stack.md).
