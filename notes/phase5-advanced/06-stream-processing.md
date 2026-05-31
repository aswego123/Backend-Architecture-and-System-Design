# Stream processing (Flink, Kafka Streams)

> Phase 5 · Tags: `streaming` `data`

## 1. The concept
Process unbounded streams of events with stateful operators (map, filter, join, window, aggregate). Engines: **Apache Flink** (most powerful), **Kafka Streams** (JVM library, simpler), **Apache Beam** (portable API).

Key concepts:
- **Event time vs processing time** — events arrive late; you want results by when they happened, not when they showed up.
- **Watermarks**: heuristic for "we've probably seen all events up to time T".
- **Windows**: tumbling, sliding, session.
- **State**: per-key state stored locally (RocksDB) and checkpointed for recovery.
- **Exactly-once**: via checkpoints + transactional sinks.

## 2. The rule / the why
Batch processing (Spark, Hadoop) is great for "process yesterday's data". Stream processing is for "react in seconds to what happened just now": fraud detection, anomaly alerts, real-time dashboards, materialized views.

## 3. Java-specific behavior
- Flink DataStream API in Java/Scala.
- Kafka Streams: lives inside your Java app, no separate cluster, scales by adding instances.
- Stateful ops need a state backend (RocksDB on local disk) + periodic checkpoint to remote storage (S3).

## 4. System design angle
- Pick Kafka Streams when "I want stream processing inside my Java service".
- Pick Flink for: large state, complex jobs, multi-source, SQL, ML pipelines.
- Event-time + watermarks is the hardest part; understand it before late-arriving data ruins your aggregations.

## 5. Common mistakes / traps
- Using processing time as default → wrong results when events are delayed.
- State that grows forever (no expiry / TTL).
- Side effects in operators (DB calls per event) → bottleneck; batch in async I/O.
- Treating stream processing as "batch but smaller" — semantics are different.

## 6. Revision checklist
- Event vs processing time: ______
- What watermarks do: ______
- One engine and when to pick it: ______
- One state-management pitfall: ______
