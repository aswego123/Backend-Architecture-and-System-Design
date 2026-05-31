# Time & order: clocks

> Phase 3 · Tags: `distributed` `theory`

## 1. The concept
- **Physical (wall) clocks**: NTP-synced, drift, can jump backwards. Not safe for ordering across nodes.
- **Lamport clocks**: per-process counter, incremented on local events and bumped on receive. Gives **partial order** (causal).
- **Vector clocks**: one counter per process per node; lets you detect concurrent vs causally-ordered events.
- **Hybrid logical clocks (HLC)**: combine physical time + logical counter — close to wall time, monotonic, captures causality.
- **TrueTime** (Spanner): hardware-bounded uncertainty — `[earliest, latest]` interval; commit waits out the uncertainty.

## 2. The rule / the why
"Now" is not a thing across machines. Ordering by physical timestamp is a footgun — clock skew breaks invariants (last-write-wins picks the wrong write).

## 3. Java-specific behavior
- `System.currentTimeMillis()` — wall clock, can jump.
- `System.nanoTime()` — monotonic, only for measuring durations on one JVM (don't compare across machines or processes).
- `Instant.now()` — wall clock, same caveat as currentTimeMillis.
- For distributed ordering, use a library or sequence (e.g., Snowflake-style IDs that embed time + node ID + counter).

## 4. System design angle
- Cassandra "last write wins" uses wall-clock timestamps → infamous data loss when clocks skew.
- Kafka offsets are per-partition logical IDs — no clock issues within a partition.
- Spanner can offer external consistency globally because of TrueTime — most systems can't.
- Snowflake IDs (Twitter) = roughly time-ordered, unique across nodes, no coordination needed.

## 5. Common mistakes / traps
- Comparing `System.nanoTime()` from different JVMs.
- Relying on NTP-sync for correctness — sync can be off by seconds.
- Using `currentTimeMillis()` as a primary key (collisions + clock jumps).
- Believing `LWW` is safe just because conflicts are rare.

## 6. Revision checklist
- Physical vs logical clocks: ______
- What vector clocks detect that Lamport doesn't: ______
- Why `nanoTime` is wrong across machines: ______
- One safe distributed ID scheme: ______
