# Domain · Product detail

**Phase:** P1
**Piece:** Level 2, platform front end on Shopify data
**Guiding principle:** the poster first, the purchase one step away
**Depends on:** [0007 · Headless storefront](../decisions/0007-headless-storefront.md)

## Purpose

The page for one poster. The buyer sees the piece as printed and as its cromo, understands what
they get (format, paper, edition, code), chooses a variant and goes to checkout. Every link to a
poster on the landing and in the calendar lands here.

## Responsibilities

- Show one poster: the print, its cromo, its race and illustrator.
- Let the buyer choose a variant and see price and availability for that variant.
- Add to cart and go to checkout.
- Show the state of the drop: on sale, sold out, upcoming, not released.

## Not this page's responsibility

- Payment, taxes, shipping cost calculation. That is Shopify's checkout.
- Codes and collection. The page only explains that the poster comes with a code.
- Inventory and price. Shopify is the source; the page reads them.

## Route and data

| Item | Value |
|---|---|
| Route | `/posters/[slug]` (`/en/posters/[slug]` in English). The slug is the race slug (`azerbaijan`) |
| Shopify product | One product per poster, linked by `handle` = slug |
| From Shopify (Storefront API) | Title, description, variants (options, price, `availableForSale`, quantity if exposed), images for SEO |
| From the platform | Race, round, date, subject, illustrator, cromo manifest, edition size, release date |
| Cache | Product data cached and revalidated by tag on Shopify `products/update` and `inventory_levels/update` webhooks. Price and availability re-checked when adding to cart |

## Content, top to bottom

1. **The poster.** The printed sheet, large, with the same `Poster` component as the landing. Hover
   (pointer) or automatic motion (touch) lifts it into its cromo. A toggle switches *Printed / Cromo*.
2. **Identity.** Subject, race and round, release date, illustrator's name.
3. **Variant selector.** One control per Shopify option. Unavailable combinations are visible but
   disabled with their reason (sold out, not offered).
4. **Price and edition.** Price of the selected variant in the market's currency. Edition size and
   how many copies are left only if the business wants it shown (open question).
5. **Primary action.** *Add to cart*, or *Buy now* straight to checkout. Disabled with a reason when
   the variant is unavailable; replaced by *Notify me* for upcoming drops (captures email, see
   [notifications.md](notifications.md)).
6. **What you receive.** Format, paper, numbered copy, the code on the back and what it does,
   shipping regions and times. The same facts as the landing's *Essentials*, from the same source.
7. **Season context.** Previous and next round, link to the calendar, progress toward the Champion
   Edition.

## Variants

Shopify options map to controls. The page does not hard-code which options exist.

| If the product has | Control |
|---|---|
| One variant | No selector, just the price |
| An option with up to 4 values (size, frame) | Segmented buttons |
| An option with more values | Select |

The cromo and the printed preview do not change with the variant unless the option is visual
(a frame). If frames are offered, the preview draws the frame around the sheet.

## States of the page

| Drop state | Page shows | Primary action |
|---|---|---|
| On sale | Everything | Add to cart |
| Sold out | Everything, sold-out label | Disabled. *Notify me of the next drop* |
| Upcoming (next round) | Silhouette of the poster, release date and countdown | *Notify me* |
| Not released (later rounds) | 404 or a placeholder with the date (open question) | None |

## Rules

1. **One click from the landing, two to checkout.** Landing → this page → checkout. *Buy now* skips
   the cart.
2. **Shopify decides price and availability.** The page never shows a price it did not read from
   Shopify in the last revalidation, and re-checks on add to cart.
3. **The art is shown, not handed over.** Images served at screen resolution, never the print
   master. Same protection as the landing (see open items in the platform's architecture doc).
4. **Works without WebGL.** The printed poster is a plain image; the cromo is an enhancement.
5. **Indexable.** Server-rendered, with title, description, Open Graph image and Product structured
   data (price, availability) per locale.

## Acceptance criteria (P1)

- [ ] Every poster link on the landing opens its product page.
- [ ] Choosing a variant updates price and availability without a full page load.
- [ ] *Add to cart* then checkout reaches Shopify's checkout with the right variant and quantity.
- [ ] A sold-out variant cannot be added to the cart.
- [ ] Changing a price or stock in Shopify shows on the page within one minute.
- [ ] The page works and the purchase completes with JavaScript limited to the variant selector
      and cart.
- [ ] Lighthouse mobile performance 85 or more on a product page.

## Open questions

- **Which variants exist?** Sizes (A3+ only, or more), frames, signed or artist's proofs? This sets
  the selector and whether the edition of 300 is per poster or per variant.
- Show copies left (*112 of 300 left*)? It creates urgency; it also reveals sales.
- Rounds not released yet: 404, or a page with the date and *Notify me*?
- Cart as a page or a drawer? Proposal: drawer, since the purchase is usually one poster.
- Bundles (a season pack, the six for the Champion Edition)? Shopify bundles or a separate product.
