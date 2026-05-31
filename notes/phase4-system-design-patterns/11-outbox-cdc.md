# Outbox pattern & CDC

> Phase 4 · Tags: `messaging` `databases` `patterns`

## 1. The concept
Problem: a service needs to update its DB *and* publish an event reliably. Dual writes (`db.save(); kafka.send();`) can fail between steps → either DB updated and no event, or event sent and DB rolled back.

**Outbox pattern**: write the event into an `outbox` table inside the same DB transaction as the data change. A separate process (relay) reads outbox rows and publishes to the broker.

**Change Data Capture (CDC)**: read the DB's transaction log directly (Postgres WAL, MySQL binlog) and convert to events. Tool: **Debezium** (publishes to Kafka).

## 2. The rule / the why
- Eliminates dual-write inconsistency without 2PC.
- CDC removes the need for explicit outbox tables at the cost of tighter coupling to DB internals.

## 3. Java-specific behavior
- Outbox: a JPA entity + a scheduled poller that publishes + marks-as-sent. Or Debezium can read the outbox table and publish.
- Debezium connectors run as Kafka Connect workers; the app doesn't change.
- Events should carry **enough payload** for consumers to act without calling back (avoid coupling).

## 4. System design angle
- Outbox enables event-driven microservices without distributed transactions.
- CDC enables: keeping a search index in sync, replicating to a warehouse, auditing, multi-region replication.
- Consumers must be idempotent (events can be redelivered).
- Schema evolution: version events, never remove fields without a deprecation window.

## 5. Common mistakes / traps
- Outbox table without an index on `published_at` → poller scans slowly as it grows.
- Dual writing "for simplicity" and accepting "occasional inconsistency" → it's never occasional under load.
- CDC events leaking internal column names → coupling consumers to your schema. Transform in the connector.
- Forgetting to compact / archive the outbox → unbounded growth.

## 6. Revision checklist
- The dual-write problem: ______
- Outbox in one sentence: ______
- One thing CDC enables: ______
- Why consumers still need idempotency: ______
