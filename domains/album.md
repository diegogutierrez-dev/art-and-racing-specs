# Domain · Album

**Phase:** P3
**Piece:** Level 3, platform
**Guiding principle:** web, not app. It is the differentiator, but it does not block launch

## Purpose

Turn the collection into something people want to open. The poster on the phone, with depth and holographic effect, and the season grid with the gaps that push toward the next drop.

## The three effects

| Effect | What it is | How it is achieved |
|---|---|---|
| **Depth** | The poster in layers (background, circuit, figure) that move when the phone tilts | Each piece is delivered in separate layers. Parallax driven by device orientation |
| **Holographic** | Shader that reacts to device orientation. It is what makes it feel like chrome | Custom shader on the top layer, fed by Device Orientation |
| **Grid** | The full season with visible gaps. The gap is what pushes toward the next drop | Season view with fixed positions; unredeemed ones are shown empty or as silhouettes |

## Stack

Three.js · custom shader · Device Orientation API · same deployment as the platform.

There is no native app. Everything runs in the phone's browser. See [tech/stack.md](../tech/stack.md) for detail.

## Responsibilities

- Render a redeemed piece with its layers and the shader.
- Respond to device orientation (and to the mouse on desktop as a fallback).
- Show the season grid with gaps.
- Link each gap to the corresponding drop (if it already came out, to the store; if not, to the landing with a date).

## What the album does not do

- It does not redeem. Redemption belongs to [account and collection](account-and-collection.md).
- It does not sell. It links to the store.
- It does not work without an account. It is a private view of the collection.

## Dependencies on the art

The album needs each piece delivered in separate layers (background, circuit, figure, plus a mask for the holographic effect). This is a requirement for the creative team and must be in the production pipeline of every drop from drop one, even though the album arrives later.

If a piece has no layers, the album shows it flat. Nothing is blocked.

## Rules

1. **It does not block launch.** Codes are issued from drop one; the album can arrive at drop six without losing anything.
2. **Graceful degradation.** Without orientation permission, without WebGL, or on desktop, the piece is shown flat and the grid works the same.
3. **Assets are served optimized.** Layers in compressed format, sized for the device. The album cannot weigh more than the landing.
4. **Orientation permission is requested in context.** Only when opening a piece, with an explanation, not on page load.

## Acceptance criteria (P3)

- [ ] A buyer opens a redeemed piece on iOS and Android and sees depth and holographic effect when tilting the phone.
- [ ] On desktop, the piece responds to the mouse.
- [ ] The grid shows redeemed pieces and gaps, and each gap links to its drop.
- [ ] A piece without layers is shown flat without error.
- [ ] The album loads in under three seconds on an average mobile connection.

## Open questions

- How many layers per piece? Three (background, circuit, figure) is the hypothesis. Define with the creative team before drop one so the art pipeline includes it.
- Is the holographic effect per piece or global? There could be "chrome" pieces and normal pieces as a rarity mechanic.
- Share a piece (image or public link)? It is virality, but it opens a public view of the album. Out of P3 unless business prioritizes it.
