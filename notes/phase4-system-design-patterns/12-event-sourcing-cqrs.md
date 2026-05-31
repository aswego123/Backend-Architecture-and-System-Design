# Event sourcing & CQRS

> Phase 4 · Tags: `architecture` `patterns`

## 1. The concept
- **Event sourcing**: store the sequence of state-changing **events** as the source of truth. Current state = fold over events. The DB becomes an append-only log.
- **CQRS** (Command Query Responsibility Segregation): separate the **write model** (commands → events) from one or more **read models** (projections optimized for queries).

Often used together but independent: you can do CQRS without event sourcing, and vice versa.

## 2. The rule / the why
- Full audit trail by construction.
- Easy to add new read models (replay events into a new projection).
- Temporal queries ("what did the cart look like at 3pm yesterday?") are trivial.
- Cost: eventual consistency between write and read sides, complexity, event schema evolution forever.

## 3. Java-specific behavior
- Axon Framework, Eventuate, or hand-rolled on Kafka.
- Snapshots (periodic state checkpoints) avoid replaying the entire log on every command.
- Event store options: Kafka (log-compacted topics), EventStoreDB, Postgres (events table).

## 4. System design angle
- Great fit: financial ledgers, order/invoice flows, anything audited.
- Bad fit: CRUD apps with no need for history — the complexity overhead doesn't pay back.
- Build read models per query — search index for full-text, denormalized table for dashboards, etc.
- Versioning events is the hardest part — never delete fields; upcast old events to new shapes in code.

## 5. Common mistakes / traps
- Storing **commands** instead of **events** (verb vs past-tense fact). Events should be `OrderPlaced`, not `PlaceOrder`.
- No snapshot strategy → 10k-event aggregate takes seconds to load.
- Coupling internal events to external consumers without a translation layer.
- Forgetting that you can never change an event's meaning — only add new versions.
- Adopting event sourcing for a simple CRUD app because it sounds elegant.

## 6. Revision checklist
- Event sourcing in one line: ______
- CQRS in one line: ______
- One good use case, one bad: ______
- Event vs command (past tense vs imperative): ______
