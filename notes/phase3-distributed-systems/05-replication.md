# Replication strategies

> Phase 3 · Tags: `distributed` `databases`

## 1. The concept
Keep copies of data on multiple nodes for durability, availability, and read scaling. Three models:

- **Single-leader (primary-replica)**: one node accepts writes, replicates to followers. Reads can hit any. Postgres, MySQL, MongoDB (default), Kafka per-partition.
- **Multi-leader**: multiple write nodes, sync among themselves. Useful for multi-region or offline-capable apps. Conflict resolution required.
- **Leaderless (Dynamo-style)**: client writes to N nodes, reads from R nodes. No special leader. Cassandra, DynamoDB, Riak.

Replication can be:
- **Synchronous**: leader waits for follower ack → no data loss on failover, but writes block on slow follower.
- **Asynchronous**: leader acks before follower writes → possible data loss on failover.
- **Semi-sync**: wait for at least one follower.

## 2. The rule / the why
Replication trades latency and complexity for durability and read throughput. Each model fits different consistency/availability goals.

## 3. Java-specific behavior
- Spring `AbstractRoutingDataSource` to route reads to replicas, writes to leader.
- Watch the **replication lag** metric — staleness window for any replica read.
- JDBC connection URLs with multiple hosts (Postgres `targetServerType=primary` / `preferSecondary`).

## 4. System design angle
- Read replicas absorb expensive analytical queries.
- Multi-region: usually one writer region + read replicas elsewhere; full multi-leader is rare and hard.
- Failover: who promotes? (manual, auto via Patroni / Orchestrator). Split-brain risk if two nodes both think they're leader.

## 5. Common mistakes / traps
- Reading from a replica right after writing to leader → stale read (replication lag).
- Async replication treated as durable → data loss on leader crash.
- Multi-leader without conflict resolution strategy → silently inconsistent.
- Failover without fencing → split-brain → diverging writes.

## 6. Revision checklist
- Three replication models in one line each: ______
- Sync vs async trade-off: ______
- Why split-brain happens: ______
- One metric to alarm on: ______
