# 0008 · Editorial content in Sanity

**Status:** `accepted` (21 October 2026)
**Owner:** development
**Supersedes:** the "External CMS for the landing: not used" line in [tech/stack.md](../tech/stack.md)

## Context

Posters, their layered art, which round each one belongs to, release dates and artists live in
code today (`apps/web/src/content/*.ts`). Publishing a poster means a commit and a deploy by the
only developer. With a drop every race week and illustrations produced on a monthly schedule, the
creative team has to publish without code. Artists' pages and social cards add more content of the
same kind.

## Options

- **Shopify only** (metaobjects and metafields). No new tool. Weak editing, no preview, awkward for
  layered art and for relations between posters, artists and rounds.
- **Prismic.** Strong page builder (slices) for marketing pages. Our pages are built in code; what
  editors change is structured data with relations, where Prismic is weaker.
- **Sanity.** Structured content with schemas in TypeScript in our repository, Studio embedded in
  the app, live preview, image CDN with resizing, a Shopify sync app, and available on the Vercel
  Marketplace (provisioned into the project, unified billing).

## Decision

Sanity for editorial content. Shopify stays the source of truth for everything commercial.

| Source | Owns |
|---|---|
| Shopify | Products, variants, price, inventory, orders |
| Sanity | Posters and their art layers, mockups, artists, seasons and the drop calendar (which poster in which round, release date), social images |
| Code | Design system, animations, cromo engine, UI strings (next-intl) |

## Consequences

- Content model in [domains/content.md](../domains/content.md).
- The landing, product pages, artists and calendar read Sanity at build/request time with tag
  revalidation from a Sanity webhook. A deploy is no longer needed to publish a poster.
- Release dates stay data: a poster can be published in Sanity days early and appears on its
  release date, because the platform derives drop status from dates (landing rule 3).
- Print masters never go into Sanity. Its asset CDN is public; web layers are screen resolution
  and protected as described in the platform's architecture document.
- One more account to move in the migration to the official project account.
