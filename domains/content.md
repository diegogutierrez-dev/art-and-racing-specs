# Domain · Content

**Phase:** P1
**Piece:** Sanity, read by the platform
**Guiding principle:** the creative team publishes without a developer
**Decision:** [0008 · Editorial content in Sanity](../decisions/0008-content-in-sanity.md)

## Purpose

Everything editorial that the site shows, in a form the creative team edits: posters and their
art, the drop calendar, artists. Commerce (price, stock, variants) is read from Shopify and never
typed in here.

## Documents

### Poster

The illustration. One per drop.

| Field | Type | Note |
|---|---|---|
| title | text, ES/EN | "Mercedes: George Russell and Kimi Antonelli" |
| subject | text | What the print shows in large type: "Mercedes", "Lewis Hamilton" |
| artist | reference → Artist | Required |
| layers | list of images, 1 to 4, back to front | Same pixel size, aligned, as the illustrator delivers them. Each with alt text ES/EN |
| palette | four colours: base, accent, ink, foil | Used by the cromo for silhouettes, dissolve edge and foil |
| foil | number 0 to 1 | Overall holographic strength. Default 0.5 |
| mockups | list of images with caption ES/EN | Poster in context (wall, frame). Shown on the product page |
| socialImage | image, optional | Overrides the generated social card |
| product | reference → Shopify product (Sanity Connect) | Price, variants and stock come from here |

Derived, not edited: aspect ratio (from the layers' size), image URLs at each size.

### Drop

A poster's place in the calendar.

| Field | Type | Note |
|---|---|---|
| season | reference → Season | |
| round | number | 1 to 24 |
| race | text ES/EN, place, race date | "GP de Azerbaiyán", "Baku", 2026-09-26 |
| slug | text | Race slug, also the product page URL and the Shopify product handle |
| poster | reference → Poster, optional | Empty means the round shows the typographic placeholder |
| releaseAt | date and time | Default: Thursday of race week. Status is derived from it |
| editionSize | number | 300 by default |

### Season

| Field | Type | Note |
|---|---|---|
| year | number | |
| championPoster | reference → Poster | The Champion Edition |
| championUnlockAt | number | How many posters unlock it |

### Artist

| Field | Type | Note |
|---|---|---|
| name, slug | text | Page at `/artistas/[slug]` |
| portrait | image | |
| bio | rich text, ES/EN | |
| links | list of label + URL | Instagram, portfolio site |
| portfolio | list of images with caption | Work outside Art and Racing, optional |

Posters by the artist are found by reference; nothing is listed twice.

## Rules

1. **Shopify numbers are never typed into Sanity.** Price, stock and variants are read live.
2. **Publishing is not releasing.** A drop can be published early; the site shows it from `releaseAt`.
3. **Layers are web resolution.** Up to 1000 px on the long side. Print masters stay outside Sanity.
4. **Every image has alt text in both languages** before the document can be published.
5. **The site updates within a minute of publishing** (webhook → tag revalidation).

## Who edits

| Role | Can |
|---|---|
| Editor (creative team, illustrator) | Create and edit posters, drops, artists; publish |
| Admin (development) | Everything, plus schema and dataset settings |

## Acceptance criteria (P1)

- [ ] An editor uploads a poster's layers, links it to a round and publishes, and the landing,
      calendar and product page show it on its release date without a deploy.
- [ ] The 13 posters delivered on 12 October and the 2026 calendar are in Sanity, and the site reads
      only from there.
- [ ] An artist page lists the artist's posters.
- [ ] Editing a poster shows the change on the site within one minute.

## Open questions

- UI strings and section copy stay in code (next-intl) for now. Move editorial copy (story,
  essentials) to Sanity later if the team edits it often.
- Do illustrators get Studio access themselves, or does someone upload for them?
