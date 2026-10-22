# Domain · Notifications (email)

**Phase:** P1 (order and newsletter emails) → P2 (production states)
**Piece:** Level 3, platform
**Guiding principle:** one voice, fired by events
**Owners:** development (sending, triggers), M. Isabel Z (content, review and tests)

## Purpose

Every email a buyer or subscriber receives comes from the platform, in Art and Racing's voice and
design, triggered by something that happened. This spec defines which emails exist, when they go
out, what they contain, how they are built and how they are tested.

## Responsibilities

- Send transactional emails on order state events ([orders.md](orders.md), Notifications).
- Send the newsletter confirmation and drop announcements to subscribers.
- Keep templates, copy and translations in one place, versioned with the code.
- Record what was sent, to whom and whether it was delivered.

## Not this domain's responsibility

- Receipts and payment emails from Shopify's checkout. See *Shopify's own emails* below.
- Deciding order states. Notifications react to state events; they never create them.

## Provider

Resend, provisioned through the Vercel Marketplace (`RESEND_API_KEY`), templates in React Email.
Already the recommendation in [tech/stack.md](../tech/stack.md).

| Item | Value |
|---|---|
| Sending domain | A subdomain of the brand domain, for example `mail.artandracing.com`, verified in Resend with SPF, DKIM and a DMARC record (`p=none` first, `quarantine` after two weeks clean) |
| From | `Art and Racing <hola@…>` for transactional, `Art and Racing <drops@…>` for announcements |
| Reply-to | A real, monitored inbox. Buyers reply to shipping emails |
| Streams | Transactional and marketing kept apart (separate from-addresses; Resend Broadcasts or a separate audience for marketing), so a complaint on announcements never hurts order emails |

## Shopify's own emails

With [0007](../decisions/0007-headless-storefront.md) the platform owns the buyer's emails.

| Shopify notification | Keep? | Why |
|---|---|---|
| Order confirmation | Off | Replaced by *Order confirmed* below, which also explains the cromo |
| Shipping confirmation and updates | Off | Replaced by *Shipped* and *Delivered* |
| Refund, cancellation | Off | Replaced by *Cancelled* and *Refund* |
| Abandoned checkout | On (Shopify) | Needs checkout data the platform does not have; branded in Shopify settings |
| Customer account emails | Depends on [0002](../decisions/0002-user-identity.md) | If Shopify accounts are used, Shopify sends them |

## Catalog

Each email is one React Email template with Spanish and English versions. The buyer's language is
the one they bought in (Shopify order `customerLocale`); subscribers keep the language they signed
up in.

Cromo moments follow [0009](../decisions/0009-cromo-without-codes.md): silhouette on payment, complete
on shipping, removed on cancel or return. There are no codes.

| # | Email | Trigger | Phase | Must contain |
|---|---|---|---|---|
| T1 | Order confirmed | State event *01 Paid* | P1 | Order number, poster(s) with thumbnail, variant, amount, shipping address, what happens next (printing, shipping times), that the cromo is already in the album as a silhouette, links to the order and the album |
| T2 | In production | State event *03 In production* | P2, optional | "Your copy is being printed", expected ship date. Only when printing takes more than a day |
| T3 | Shipped | State event *05 Shipped* | P1 (manual state) | Carrier, tracking number, tracking link, estimated delivery, that the cromo is now complete |
| T4 | Did it arrive safely? | State event *06 Delivered* | P2 | Care for hanging it, how to report damage by replying |
| T5 | Cancelled | State event *Cancelled* | P1 | What was cancelled, that its cromos left the album, refund handled by Shopify and how long it takes |
| T6 | Refund / return | State event *Return* or Shopify `refunds/create` | P2 | Amount, that the returned posters' cromos left the album and stop counting |
| T7 | Champion Edition unlocked | Shipped posters of the season reach the unlock count | P2 | Congratulations, the goal reached, that it is free, button to claim it (confirms the shipping address) |
| A1 | Sign-in link | Buyer asks to open their account or album | P1 if accounts are passwordless ([0002](../decisions/0002-user-identity.md)) | One-time link, expiry, what to do if it wasn't them |
| M1 | Confirm your subscription | Email captured on the landing or a product page | P1 | Double opt-in link, what they will receive and how often |
| M2 | New drop is out | Drop release (Thursday of race week) | P1 | The poster, the race, edition size, link to its product page |
| M3 | Race week | Monday of race week, to subscribers | P2 | The race and its city, release day, edition size, that it is not reprinted. No art before release |
| M4 | Close to the Champion Edition | Buyer is one or two shipped posters from the unlock count | P2 | Progress, what the Champion Edition is, the next drop |
| M5 | Welcome | Right after M1 is confirmed | P1 | How drops work (one per race, Thursday, 300 numbered copies, no reprints), the album, the Champion Edition, link to the season |
| M6 | Last copies | Once per drop, when stock falls under 10% of the edition | P2 | The poster, copies left, that it is not reprinted. Only to subscribers who don't own it |
| M7 | More from the season | Ten days after a delivery for buyers; at most monthly for other subscribers | P2 | Up to four released posters with copies left that the reader doesn't own, copies left on each, that each counts toward the Champion Edition |

Rules for content:

1. One purpose per email. The subject says it: *Order #1042 confirmed*, *Your Baku poster has
   shipped*.
2. No marketing in transactional emails beyond one line about the next drop.
3. Links in emails never carry personal data in the URL; sign-in links carry a one-time token only.
4. Marketing is capped at two emails a week per subscriber, drop announcements included. When M6 or M7
   would break the cap, it waits or is skipped; M2 always goes out.
5. Tone: a club with Formula 1 heritage, in the spirit of Gran Turismo. Subscribers and buyers are members;
   marketing speaks of race weeks, circuits and the collection. Short and concrete. Transactional subjects stay plain so they are found in the inbox.
6. Every marketing email has a one-click unsubscribe (`List-Unsubscribe` header and a link);
   transactional emails do not.

## Design

- Same tokens as the landing (colours, type scale, spacing), compiled to inline styles by React
  Email. Fonts: Inter where supported, Helvetica and Arial fallback.
- 600 px wide, single column, works in dark mode (no white-on-transparent logos; tested both ways).
- Images: poster thumbnails at 2× from the platform's image route, with `alt` text. The email must
  read correctly with images off.
- A plain-text part generated for every email.
- One shared layout: header with logo, body, footer with address (required for marketing), help
  link and, for marketing, unsubscribe.

## Sending

- Triggered by inserting a state event (or a subscriber/drop event), never by polling
  ([orders.md](orders.md) rule 4).
- Sent from a background step after the database commit, so a failed send never rolls back the
  state. Retries with backoff up to 3 times; after that an alert ([tech/stack.md](../tech/stack.md),
  Observability).
- Idempotent: Resend's `Idempotency-Key` = `{order_id}:{state}` (or `{subscriber}:{drop}`), so a
  retried webhook or job never sends twice.
- Each send is stored: template, locale, recipient, Resend message ID, status updated by Resend's
  webhooks (`delivered`, `bounced`, `complained`).
- Hard bounces and complaints suppress the address for marketing. Transactional still goes out
  unless the address hard-bounced.

## Testing

Owned with M. Isabel Z. Three layers, in this order:

1. **Preview.** `email dev` (React Email) shows every template in both languages with fixture
   data, including edge cases: three posters in one order, long names, a framed variant, a missing
   thumbnail. Copy and design are reviewed here first.
2. **Sandbox sends.** In development and preview environments every email goes to Resend's test
   addresses (`delivered@resend.dev`, `bounced@resend.dev`, `complained@resend.dev`) or to an
   allowlist of team inboxes. Real customer addresses are never reachable outside production.
3. **Client check.** Before launch and whenever the layout changes, one send per template to:
   Gmail (web, Android), Apple Mail (macOS, iOS), Outlook (web and Windows desktop), in light and
   dark mode. Checklist per template: subject and preview text, layout, images off, links,
   plain-text part, unsubscribe where it applies, language.

An end-to-end test on the Shopify development store: place an order → T1 arrives; mark *shipped*
in the panel (or the admin in P1) → T3 arrives with the tracking link.

## Acceptance criteria (P1)

- [ ] Sending domain verified with SPF, DKIM and DMARC; first emails land in the inbox, not spam,
      in Gmail and Outlook.
- [ ] T1, T3, T5, M1 and M2 exist in Spanish and English and pass the client check. The rest of the
      catalog is designed and reviewed at `/correos`.
- [ ] A test order on the development store sends T1 once, even if the webhook arrives twice.
- [ ] Shopify's order, shipping and cancellation notifications are off in the live store.
- [ ] Every send is recorded with its Resend status.
- [ ] Unsubscribing from M2 stops announcements and does not affect order emails.

## Open questions

- Brand domain and inboxes: the sending domain depends on the final name
  ([0006](../decisions/0006-trademark-registration-in-the-us.md)) and on the official project
  account.
- Newsletter: Resend Audiences/Broadcasts, or keep subscribers in the platform's table and send
  through Resend? Proposal: platform table as the source, Resend Broadcasts for sending.
- T2 (in production): send it, or does it add noise? Business decides.
- Do announcements go to every subscriber or only to those who chose a team or driver?
