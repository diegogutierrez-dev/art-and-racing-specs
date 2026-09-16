# Decisions

Architecture decision records (ADRs). Each decision is a numbered file. A decision is not edited once accepted: if it changes, a new one is created that supersedes it.

## Statuses

| Status | Meaning |
|---|---|
| `pending` | Blocks work. Has an owner and a deadline |
| `proposed` | There is a recommendation, it has not been accepted yet |
| `accepted` | In force. Specs assume it |
| `rejected` | Evaluated and not taken. The reason is kept |
| `superseded` | Another decision replaces it. The new one is linked |

## Index

| ID | Decision | Status | Blocks |
|---|---|---|---|
| [0001](0001-payment-gateway-and-jurisdiction.md) | Payment gateway and jurisdiction | `pending` | P1 |
| [0002](0002-user-identity.md) | User identity | `pending` | P1 |
| [0003](0003-landing-and-platform-in-one-deployment.md) | Landing and platform in one deployment, outside Shopify | `proposed` | P1 |
| [0004](0004-order-state-as-event-log.md) | Order state is an event log, not a field | `proposed` | P1 |
| [0005](0005-album-does-not-block-launch.md) | The album is P3 and does not block launch | `accepted` | — |
| [0006](0006-trademark-registration-in-the-us.md) | Trademark registration in the US | `accepted` | Final name |

## Template

```markdown
# NNNN · Title

**Status:** pending | proposed | accepted | rejected | superseded
**Owner:** name
**Deadline:** YYYY-MM-DD
**Blocks:** P1 | P2 | P3 | —

## Context
## Options
## Decision
## Consequences
```
