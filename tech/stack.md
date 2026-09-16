# Tech stack · advice

> This is guidance. Specs describe behavior and domain; the stack is the means and can change without anything in [domains/](../domains/) changing. If something here contradicts a spec, the spec wins.

## Summary

| Layer | Recommendation | Why |
|---|---|---|
| Framework | Next.js (App Router) with TypeScript | One deployment for landing, account, panel, and album. Server Components for the landing, Server Actions for the panel |
| Hosting | Vercel | Zero config for Next.js, previews per PR, Fluid Compute for webhooks and cron |
| Styling | Tailwind CSS v4 + shadcn/ui | Speed on the panel (tables, forms) without giving up control on the landing |
| Database | Managed Postgres (Neon via Vercel Marketplace) + Drizzle ORM | Three tables and an event log. Postgres is more than enough and does not get in the way |
| Transactional email | Resend (via Marketplace) with React Email templates | Five emails per order, fired by event |
| Store | Shopify Basic | Catalog, checkout, taxes. See [store.md](../domains/store.md) |
| Buyer auth | Depends on [decision 0002](../decisions/0002-user-identity.md) | Shopify Customer Account API, or Better Auth / Clerk with magic link |
| Production auth | Separate from the buyer's. Clerk or Better Auth with roles | A different table from the buyer's |
| Album assets | Vercel Blob | Layers per piece, served with long cache |
| 3D and shaders | Three.js via react-three-fiber + drei | P3 only. Lazy loaded on the album route |
| Configuration | `vercel.ts` with `@vercel/config` | Typed, crons and headers in a single file |

## Technical principles

1. **One deployment.** Landing, account, panel, and album live in the same Next.js project. See [decision 0003](../decisions/0003-landing-and-platform-in-one-deployment.md).
2. **Node.js, not Edge.** Webhooks, Server Actions, and cron run on the default Node.js runtime (Fluid Compute). Do not use `runtime = 'edge'`; it adds nothing here and removes compatibility.
3. **Server-first.** The landing and the account are Server Components. Client only for what is interactive: redemption, panel, album.
4. **Marketplace before custom infrastructure.** Postgres, email, auth, and monitoring are provisioned from the Vercel Marketplace. Nothing is set up by hand until a real drop justifies it.
5. **Types from the database.** Drizzle generates the types; state enums live in one place and are imported everywhere.

## Suggested project structure

```
app/
  (landing)/            # level 1 · public
    page.tsx
    drops/[slug]/
  (account)/            # level 3 · buyer
    account/
      orders/
      collection/
      redeem/
  (production)/         # level 3 · internal, protected route
    panel/
  (album)/              # level 3 · P3, lazy loaded
    album/[season]/
  api/
    webhooks/shopify/   # orders/paid, orders/cancelled, refunds/create
    redeem/             # redemption endpoint
db/
  schema.ts             # orders, state_events, codes, drops, pieces
  migrations/
lib/
  shopify/              # Storefront API client, HMAC verification
  states/               # valid transition machine
  email/                # templates and sending
emails/                 # React Email
vercel.ts
```

Route groups follow the three levels in [docs/01-system.md](../docs/01-system.md).

## Shopify integration

| Need | Tool | Note |
|---|---|---|
| Receive `orders/paid` | Route Handler in `app/api/webhooks/shopify` | Verify HMAC with the webhook secret before reading the body. Respond 200 fast, process inside the same handler (Fluid Compute allows it) |
| Idempotency | Unique index on `shopify_order_id` | A second webhook does `INSERT ... ON CONFLICT DO NOTHING` |
| Drop's product on the landing | Storefront API (GraphQL) | Cache with `use cache` and revalidate by tag when the drop changes |
| Identity (if decision 0002 = A) | Customer Account API | OAuth with PKCE. The platform session stores the token |
| Sync tracking number (optional) | Admin API, `fulfillmentCreateV2` | Only as an outgoing mirror when marking *shipped* |
| Store theme | Shopify theme with the same design tokens | Same fonts, colors, and spacing as the landing |

## Database

Minimal schema, one to one with the domains:

| Table | Domain | Note |
|---|---|---|
| `drops` | Landing, collection | slug, date, season, grid position |
| `pieces` | Collection, album | drop, illustrator, image, layers (Blob URLs) |
| `orders` | Orders | `shopify_order_id` unique, `current_state` projected |
| `state_events` | Orders | Log. See [decision 0004](../decisions/0004-order-state-as-event-log.md) |
| `codes` | Account and collection | unique code, order, piece, state, account |
| `accounts` | Account | Only if decision 0002 = B. If A, reference to the Shopify customer |
| `production_users` | Production | Roles `production` and `admin` |
| `subscribers` | Landing | Captured email, or delegate to Resend Audiences |

The valid transition machine lives in code (`lib/states`), not in the database. A `CHECK` on `state_events.state` limits values to the enum.

## Emails

Resend with React Email. One template per state that notifies ([orders.md](../domains/orders.md), Notifications section). Sending fires on inserting the state event. If sending fails, it is retried; the state is not reverted.

For the sender, own domain verified in Resend before drop one.

## Redemption and protection

- Redemption endpoint as an authenticated Server Action or Route Handler.
- Rate limit per account and per IP. Vercel Firewall with a rate limit rule on the redemption route, or Upstash Redis via Marketplace if custom logic is wanted.
- BotID on the redemption form only if abuse shows up.
- The code never travels in a query string in email links; the QR points to `/redeem?c=CODE` and the page reads it server-side and passes it to the form.

## Detecting *delivered*

P2: manual from the panel.
Later: a cron (`vercel.ts` → `crons`) that queries the carrier's API for orders in *shipped* and marks *delivered*. Each carrier is an adapter in `lib/carriers`.

## Album (P3)

- `react-three-fiber` + `drei` for the canvas. Custom holographic shader in GLSL.
- Device Orientation API with permission requested in context (iOS requires it in a user gesture).
- Layers as compressed textures (KTX2 or WebP depending on support) in Vercel Blob.
- The album route is a separate group with `dynamic import` so Three.js does not enter the landing or panel bundle.
- Degradation: without WebGL or without permission, flat image with CSS.

## Environments and variables

| Variable | Use |
|---|---|
| `DATABASE_URL` | Postgres. Injected by the Marketplace |
| `SHOPIFY_STORE_DOMAIN` | Store |
| `SHOPIFY_STOREFRONT_TOKEN` | Storefront API |
| `SHOPIFY_WEBHOOK_SECRET` | HMAC verification |
| `SHOPIFY_ADMIN_TOKEN` | Only if fulfillment is synced |
| `RESEND_API_KEY` | Email. Injected by the Marketplace |
| `BLOB_READ_WRITE_TOKEN` | Album assets |
| `AUTH_*` | Per the chosen auth provider |

Managed with `vercel env`. Three environments: development, preview, production. The preview store points to a Shopify development store, never the real one.

## Observability

- Vercel Logs and Observability for functions and webhooks.
- Minimum alerts: webhook responding anything other than 200, email failing three times, order more than N days in *shipped*.
- No business dashboards in the platform. The Shopify admin is for that.

## What is not used

| Not this | Why |
|---|---|
| Edge runtime | No benefit here and with compatibility restrictions |
| Native app | Everything is web. See [MANIFESTO.md](../MANIFESTO.md) |
| Blockchain / NFTs | The code is a row in a table |
| Microservices | Three tables and a log do not justify more than one deployment |
| External CMS for the landing | Drops and pieces live in the same database. If the creative team needs to edit without code, evaluate in P2 |
| Redis as a database | Only for rate limiting if needed |

## When to revisit this document

- When [decision 0002](../decisions/0002-user-identity.md) is made: it fixes the auth provider.
- When closing P1: confirm Shopify Basic and Vercel Hobby/Pro are still enough.
- Before P3: review texture formats and Device Orientation support in the browsers of the moment.
