# Domain · Landing

**Phase:** P1
**Piece:** Level 1, the entry point
**Guiding principle:** it is the entry point, not a brochure

## Purpose

The landing has to do three different jobs with a single design. If it only tells the story, it does not work.

| Job | What it does | Where it leads |
|---|---|---|
| **Tell** | The drop of the week, the illustrators, why this exists | Stays on the landing |
| **Sell** | Direct route to the product without friction. Checkout is one click away | Store |
| **Retain** | Entry to the account: see my collection, redeem a code | Platform |

## Responsibilities

- Show the current drop with a direct route to the product in the store.
- Tell the narrative: illustrators, season, why the project exists.
- Capture email to announce the next drop.
- Give access to the account (sign in, see collection, redeem code).
- Show past drops (sold out or not) as part of the season narrative.

## Not the landing's responsibility

- Full catalog or cart. That is the store.
- Authentication. The landing links to the account; the platform authenticates.
- Long editorial content. A landing, not a blog.

## Rules

1. **One click to the product.** From any view on the landing, the current drop's product is one click away. Checkout, two.
2. **Email capture blocks nothing.** It is an invitation, not a wall.
3. **The current drop is dynamic.** No deployment is needed to change drops. The current drop is defined by data (publication date, product state in Shopify, or a field in the platform).
4. **Same session.** If the buyer has a session on the platform, the landing knows it and shows their account access, not the sign-in button.

## Entities

| Entity | Source | Use on the landing |
|---|---|---|
| Drop | Platform | Which one is current, which ones passed, which one is next |
| Product | Shopify | Image, price, availability, purchase link |
| Illustrator | Platform | Name, short bio, pieces in the season |
| Subscriber | Platform (or email provider) | Captured email |

## Related decision

[0003 · Landing and platform in one deployment, outside Shopify](../decisions/0003-landing-and-platform-in-one-deployment.md). Inside Shopify is faster and has no extra cost, but the design is limited by the theme. Outside gives full control and is where the platform lives, at the cost of maintaining two deployments.

## Acceptance criteria (P1)

- [ ] A new visitor sees the current drop and reaches the product in the store with one click.
- [ ] A visitor can leave their email and receives confirmation.
- [ ] A buyer with an account sees the route to their collection from the landing.
- [ ] Changing the current drop requires no deployment.

## Open questions

- Are past drops shown as a browsable archive or only as the season grid? Affects how much of the narrative lives on the landing vs. in the album.
- Email capture with an external provider (Shopify list, Resend, etc.) or our own table? See [tech/stack.md](../tech/stack.md).
