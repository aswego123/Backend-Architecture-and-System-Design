# CAP & PACELC

> Phase 3 · Tags: `distributed` `theory`

## 1. The concept
**CAP** (Brewer): in the presence of a network **P**artition, a system must choose between **C**onsistency (every read sees the latest write) and **A**vailability (every request gets a non-error response).

**PACELC** (extends CAP): if **P**artitioned, choose **A** vs **C**. **E**lse (normal operation), choose **L**atency vs **C**onsistency.

## 2. The rule / the why
- CAP is often misstated. It's *not* "pick 2 of 3" — partitions are unavoidable, so the real choice is C vs A *during* a partition.
- PACELC matters more in practice: even with no partition, strong consistency costs latency (cross-node coordination).

## 3. Java-specific behavior
- ZooKeeper, etcd: CP (refuse writes on partition).
- Cassandra, Riak, DynamoDB: AP (accept writes, reconcile later).
- A Java client must handle either: timeouts and retries for CP, conflict resolution / read-repair for AP.

## 4. System design angle
| System | Partition | Else |
|---|---|---|
| Postgres (single leader) | CP | EC (low latency at cost of consistency on replicas) |
| Cassandra | AP | EL |
| DynamoDB | AP/CP (tunable) | EL/EC |
| Spanner | CP | EC (uses atomic clocks to keep latency reasonable) |

- "Eventually consistent" = will converge if writes stop. Says nothing about how long.
- Most real systems are tunable per request (Cassandra consistency level, DynamoDB strongly-consistent reads).

## 5. Common mistakes / traps
- "We're CA" — no, you're CP that hasn't been partitioned yet.
- Assuming "strong consistency" is free — it's a latency tax even with no failures.
- Eventual consistency advertised, but app logic assumes read-your-writes.
- Forgetting that **clients** are part of the system — a CP store with an AP cache in front is AP overall.

## 6. Revision checklist
- State CAP correctly: ______
- What PACELC adds: ______
- One CP system, one AP system: ______
- Why "CA" doesn't exist in a real network: ______
