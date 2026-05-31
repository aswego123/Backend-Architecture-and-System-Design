# Partitioning & sharding

> Phase 3 · Tags: `distributed` `databases`

## 1. The concept
Split data across nodes so one node doesn't hold (or serve) everything.

Strategies:
- **Range partitioning**: contiguous ranges per shard. Great for range scans. Risk: hot ranges (sequential keys → one hot shard).
- **Hash partitioning**: `hash(key) % N` or consistent hashing. Spreads load evenly. Bad for range scans.
- **Directory / lookup**: explicit mapping table. Flexible but extra hop.
- **Composite**: partition by `tenant_id` then hash within.

## 2. The rule / the why
Vertical scaling has a ceiling. Sharding is how you keep growing — at the cost of cross-shard joins, transactions, and rebalancing complexity.

## 3. Java-specific behavior
- App-level sharding: pick a shard in code → route JDBC URL.
- ShardingSphere (Apache) for JDBC-level sharding.
- Cassandra/Mongo/Elasticsearch shard automatically; you choose the partition key.

## 4. System design angle
- **Choosing a partition key**: high cardinality, uniform distribution, aligned with query patterns. `user_id` is usually good; `country` is usually bad.
- **Hot keys**: one celebrity user, one popular item → swamps one shard. Mitigations: split, replicate, in-memory cache, write-buffering.
- **Rebalancing**: consistent hashing + virtual nodes moves a small fraction of data when nodes join/leave.
- **Cross-shard queries** (joins, aggregations): scatter-gather; aggregate in app or use a separate analytics store.

## 5. Common mistakes / traps
- Picking a low-cardinality partition key → uneven distribution.
- Sharding too early ("YAGNI" — vertical scaling often goes further than you think).
- Sharding the OLTP DB then realizing analytics needs cross-shard joins.
- Forgetting that transactions don't cross shards (in most systems).
- No plan for rebalancing → adding a shard requires downtime.

## 6. Revision checklist
- Range vs hash partitioning trade-off: ______
- Pick a good partition key for an e-commerce orders table: ______
- One hot-key mitigation: ______
- Why cross-shard joins hurt: ______
