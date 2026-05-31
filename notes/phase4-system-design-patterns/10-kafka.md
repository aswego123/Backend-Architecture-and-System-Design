# Kafka deep dive

> Phase 4 · Tags: `messaging` `kafka`

## 1. The concept
Kafka = distributed append-only **commit log**, partitioned and replicated.

Core nouns:
- **Topic**: named stream of records.
- **Partition**: ordered, immutable log; the unit of parallelism and ordering. A topic has N partitions.
- **Offset**: position within a partition. Consumers track their own offsets.
- **Producer**: appends records to partitions (chosen by key hash or round-robin).
- **Consumer group**: shares partition consumption — Kafka assigns each partition to exactly one consumer in the group at a time.
- **Replication**: each partition has a leader + N-1 replicas; producers/consumers talk to the leader.

## 2. The rule / the why
Order, durability, replay, and high write throughput on commodity hardware. Decouples producers (writers) from consumers (readers) over time, and lets you fan out to multiple consumer groups without re-sending data.

## 3. Java-specific behavior
- `org.apache.kafka:kafka-clients` for raw producer/consumer.
- Spring for Apache Kafka: `@KafkaListener`, `KafkaTemplate`.
- Avro/Protobuf + Schema Registry for typed records.
- Producer config that matters: `acks=all`, `enable.idempotence=true`, `linger.ms`, `batch.size`, `compression.type`.
- Consumer config: `auto.offset.reset` (earliest/latest), `enable.auto.commit=false` + manual commit after processing.

## 4. System design angle
- **Partition count = max parallelism** for one consumer group. Pick generously up front; reducing later is painful.
- **Key choice** determines order and load balance. Same key → same partition → in-order delivery for that key.
- **Retention**: time-based (`log.retention.hours`) or size-based. Log compaction keeps the latest value per key forever (great for state snapshots).
- **Exactly-once**: producer idempotence + transactions + consumer reading from same Kafka. Outside Kafka, you still need idempotent consumers.
- Use **Kafka Streams** for stateful processing (joins, aggregations) inside the JVM; **Flink** for heavier needs.

## 5. Common mistakes / traps
- Too few partitions → can't scale consumers.
- Too many partitions → metadata overhead, slow leader election.
- `auto.commit=true` + slow processing → commits offsets for messages you haven't processed → data loss on crash.
- Hot key → one partition does all the work.
- Treating Kafka as a queue (one consumer) and being surprised that ordering only holds per partition.

## 6. Revision checklist
- Topic, partition, offset in one line each: ______
- Why same key → same partition: ______
- Two producer configs for durability: ______
- The "auto commit + slow processing" data loss path: ______
