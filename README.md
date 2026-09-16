# Art and Racing · Specs

Specifications, domains, and manifesto for the technical layer of Art and Racing: landing, store, and collection platform.

This repository contains no code. Only Markdown. It is the source of truth for **what** gets built and **why**. The **how** lives in the application repository, which is subordinate to what is here.

## Start here

| Document | What it answers |
|---|---|
| [MANIFESTO.md](MANIFESTO.md) | What the platform is, what it is not, and the principles that order everything else |
| [docs/00-scope.md](docs/00-scope.md) | What happens inside the platform and what happens outside it |
| [docs/01-system.md](docs/01-system.md) | The three pieces of the system and how they connect |
| [docs/02-phases.md](docs/02-phases.md) | Three-phase plan: sell, operate, collect |
| [docs/03-glossary.md](docs/03-glossary.md) | Shared vocabulary between business and development |

## Domains

Each domain is an area of the system with its own vocabulary, rules, and responsibilities. A domain should not know more about another domain than what that domain's spec declares as its interface.

| Domain | Phase | Description |
|---|---|---|
| [Landing](domains/landing.md) | P1 | The entry point. Tell, sell, retain |
| [Store](domains/store.md) | P1 | Catalog and checkout on Shopify |
| [Payments](domains/payments.md) | P1 | Gateway, currencies, and jurisdiction |
| [Orders](domains/orders.md) | P1 → P2 | Order states, handoff, and exceptions |
| [Production](domains/production.md) | P2 | Production panel over the same state record |
| [Account and collection](domains/account-and-collection.md) | P2 | Identity, codes, redemption, and collection |
| [Album](domains/album.md) | P3 | The digital album with depth and holographic effect |
| [Trademark](domains/trademark.md) | Before P1 | US trademark registration (decided). Costs and operations to research |
| [Costs](domains/costs.md) | Cross-cutting | What the technical layer costs |

## Decisions

Architecture decisions that define scope live in [decisions/](decisions/) as ADRs (Architecture Decision Records). Two are **pending and block phase one**:

- [0001 · Payment gateway and jurisdiction](decisions/0001-payment-gateway-and-jurisdiction.md)
- [0002 · User identity](decisions/0002-user-identity.md)

## Tech stack

[tech/stack.md](tech/stack.md) collects the recommended ecosystem (Next.js, Vercel, Tailwind, Shopify) as advice. It is not a spec. It can change without anything above changing.

## Contributing to this repository

- Every scope change goes through the [MANIFESTO.md](MANIFESTO.md) first. If the manifesto does not allow it, it does not go in.
- A new decision is recorded as an ADR in [decisions/](decisions/) with its status (`pending`, `proposed`, `accepted`, `rejected`, `superseded`).
- Every domain has an **Open questions** section. If something is unresolved, it goes there, not in a code comment.
- Figures (fees, rates, prices) carry a verification date. If the date is more than six months old, reconfirm before using it.

## Origin

This repository comes from the presentation *Technical scope: landing, store, and platform* (working document, September 2026, when the project was still called Afiches F1). That presentation covers only the technical layer; the business model, the art, and the commercial strategy are defined by the team outside this repository.
