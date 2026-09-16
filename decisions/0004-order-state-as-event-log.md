# 0004 · Order state is an event log, not a field

**Status:** `proposed`
**Owner:** development
**Deadline:** before P1
**Blocks:** P1

## Context

The order goes through seven states and three exceptions. Production marks them, the buyer sees them, emails reflect them. It has to be decided whether the state is a field that gets overwritten or a history of events. Detail in [domains/orders.md](../domains/orders.md).

## Options

### A `state` field on the order

- Simple. One column.
- History is lost: who marked what and when. To audit a reprint or a complaint there is no trail.

### A log of state events

- A `state_event` table with order, state, author, note, and date. The current state is the last event (or a projected column).
- Full history. Audit for free. Exceptions are events like any other.
- A bit more code to project the current state.

## Proposed decision

Event log. The order has `current_state` as a projection of the last event, for fast queries, but the truth is in the log.

## Consequences

- A state is never deleted; one is added.
- The production panel shows the history per order.
- Exceptions (cancellation, reprint, return) are states in the same log, not separate tables.
- Emails fire on inserting an event, not on changing a field.
