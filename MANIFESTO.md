# Manifesto

This document says what the platform is, what it is for, and what it is not. Everything else in this repository (specs, decisions, technical advice) is subordinate to it. If a spec contradicts the manifesto, the spec is wrong.

## What this is

Art and Racing sells printed posters in drops. Every poster carries a unique code. With that code, the buyer claims the piece in their account and sees it inside their digital collection for the season. Every slot the buyer has not filled yet is a placeholder: an empty frame that announces the next piece.

The platform is the technical layer that makes that cycle possible: arrive, buy, receive, claim, collect.

## Principles

### 1. The platform stops at the order

Everything from the landing page to the paid order happens inside the platform. Everything after that (printing, packing, shipping, delivery) happens in the physical world.

The platform records the physical world. It gives production a panel to report progress, it tells the buyer what is happening, and it activates the code when the poster arrives. It does not print, pack, ship, or answer for the quality of a print.

This line is written down so that everyone on the team knows which problems get solved with code and which ones get solved on the production floor.

### 2. Three pieces, one experience

The system is three pieces: the landing, the store, and the platform. Each one has a job.

The buyer goes through all three in a single session and should never feel like they switched products. That means one domain and one visual identity across the three.

### 3. One record of state

The state of an order lives in one place. The production panel writes it. The buyer's account reads it. Emails reflect it. Nothing gets updated twice and there is no parallel spreadsheet.

When an exception shows up (cancellation, reprint, return) it is a new state, modeled from day one.

### 4. First sell, then operate, then collect

The phases are in that order for a reason. The digital album is the differentiator, and building it before selling the first poster would be the classic mistake.

Codes are issued from drop one. The album can arrive at drop six without losing anything, because the code already exists and is already tied to the account.

### 5. Minimal backend

A table of codes, a redemption endpoint, a table of order states. No blockchain, no native app, no heavy infrastructure. If a piece of infrastructure is not justified by a real drop, it does not get built.

### 6. Boring where it should be boring

The production panel is a table, filters, and state buttons. No dashboards. The landing and the album are where the design effort goes.

### 7. Blocking decisions are made before writing code

Two decisions define the scope of everything else and cannot be changed cheaply later:

- **A. Payment gateway and jurisdiction.** Colombia or a US entity. Defines fees and who the store can sell to.
- **B. User identity.** Shopify accounts or our own auth. Defines the scope of the platform.

Each one has a person and a date assigned. Without that, phase one does not start. They are documented in [decisions/](decisions/).

### 8. Technical advice is advice

The recommended stack (Next.js, Vercel, Tailwind and their ecosystem) lives in [tech/](tech/) as guidance. Specs describe behavior and domain. The stack can change without the spec changing.

## What this is not

- A marketplace. The store sells its own drops.
- A native app. Everything is web.
- An NFT platform. The code is a row in a table.
- A production tool. Production gets a panel; the work happens on the floor.
- Legal or accounting advice. Where a document touches those topics, it says so and points to someone who is.

## How to use this repository

1. Read this manifesto.
2. Read [docs/00-scope.md](docs/00-scope.md) and [docs/01-system.md](docs/01-system.md).
3. Review the pending decisions in [decisions/](decisions/).
4. Go to the domain you are going to build in [domains/](domains/).
5. Consult [tech/stack.md](tech/stack.md) only when you are about to write code.
