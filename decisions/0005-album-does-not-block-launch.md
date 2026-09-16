# 0005 · The album is P3 and does not block launch

**Status:** `accepted`
**Owner:** development and business
**Deadline:** —
**Blocks:** —

## Context

The digital album (depth, holographic, grid) is the product's differentiator. The temptation is to build it first. Detail in [domains/album.md](../domains/album.md).

## Decision

The album is phase 3. Codes are issued from drop one and the basic collection arrives in P2. The album can arrive at drop six without losing anything, because every poster already has its code and every code is already in its account.

Building it before selling the first poster would be the classic mistake.

## Consequences

- P1 and P2 do not depend on Three.js, shaders, or Device Orientation.
- The art pipeline must deliver layers from drop one, even if they are not used until P3. If it does not deliver them, the album shows the piece flat.
- The collection in P2 is a simple grid. The album replaces it without changing the data model.
