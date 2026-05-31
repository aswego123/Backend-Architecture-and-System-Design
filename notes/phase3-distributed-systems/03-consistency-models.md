# Consistency models

> Phase 3 · Tags: `distributed` `theory`

## 1. The concept
A consistency model is a contract between the storage system and the client about what reads can return given concurrent writes.

Strong → weak:
- **Linearizable**: there exists a single global order; every operation appears to happen instantly at some point between its invocation and response. "Looks like one node."
- **Sequential**: a total order exists, but doesn't have to respect real time.
- **Causal**: operations causally related appear in order; concurrent ones can differ across observers.
- **Read-your-writes**: a session always sees its own writes.
- **Monotonic reads**: a session never sees older data after newer data.
- **Eventual**: if writes stop, all replicas eventually converge.

## 2. The rule / the why
Stronger = easier to reason about, slower / less available. Pick the weakest model your invariants allow.

Many bugs come from mismatched assumptions: app code assumes linearizable, DB only gives eventual.

## 3. Java-specific behavior
- JDBC + single-leader Postgres = linearizable per-key (on the leader); reads from replicas may be stale.
- DynamoDB: eventual by default; pass `ConsistentRead=true` for strongly-consistent reads (costs more, higher latency).
- Cassandra: per-query consistency level (`ONE`, `QUORUM`, `ALL`, `LOCAL_QUORUM`).

## 4. System design angle
- User-facing flows usually need **read-your-writes** at minimum (post a comment → see it on reload).
- "Stale read" tolerance grows with cache layers — design with that in mind.
- Session stickiness or write-through invalidation can promote weaker models to read-your-writes for one user.

## 5. Common mistakes / traps
- Treating "eventual consistency" as "almost immediate" — under load, lag can be seconds.
- Cross-key invariants ("balance > 0 across accounts") need stronger guarantees than per-key linearizability.
- Confusing isolation level (transactional) with consistency model (replication). They're related but distinct.

## 6. Revision checklist
- Rank these: causal, linearizable, eventual: ______
- Define read-your-writes: ______
- Why per-key linearizable isn't enough for "balance > 0": ______
