# Quorums & read repair

> Phase 3 · Tags: `distributed`

## 1. The concept
In leaderless systems, client writes to **W** replicas and reads from **R** replicas out of **N** total.

If `R + W > N`, every read sees at least one node with the latest write → **quorum**.

**Read repair**: when reads see disagreeing replicas, the client/coordinator pushes the latest version to the laggards.

**Anti-entropy**: background process (Merkle trees) reconciles divergent replicas.

## 2. The rule / the why
- `R + W > N` is the simplest way to get "strong-ish" reads in an AP system.
- Tunable: prefer fast writes? `W=1, R=N`. Prefer fast reads? `W=N, R=1`.
- Read repair makes the system self-healing without full anti-entropy on every miss.

## 3. Java-specific behavior
- Cassandra Java driver: per-query `ConsistencyLevel.QUORUM`, `ONE`, `ALL`, `LOCAL_QUORUM`.
- DynamoDB: `ConsistentRead` flag bypasses eventual-consistent fast path.

## 4. System design angle
- Geo-replicated systems use `LOCAL_QUORUM` (quorum within the local datacenter) → low latency + reasonable durability.
- `R + W > N` doesn't give you linearizability — concurrent writes can still race. It just bounds staleness.
- Sloppy quorum + hinted handoff (Dynamo): accept writes on substitute nodes during partition; replay later.

## 5. Common mistakes / traps
- Setting `W=1` to make writes fast, then being surprised by data loss after node failure.
- Believing quorum gives transactions — it doesn't (no atomicity across keys).
- Forgetting clock skew makes "latest version" ambiguous in last-write-wins systems.

## 6. Revision checklist
- The quorum inequality: ______
- What read repair does: ______
- Why `R+W>N` ≠ linearizability: ______
- `LOCAL_QUORUM` vs `QUORUM`: ______
