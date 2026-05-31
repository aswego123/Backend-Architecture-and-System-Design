# LSM trees vs B+ trees

> Phase 1 · Tags: `dsa` `databases` `storage`

## 1. The concept
Two ways to keep sorted data on disk.

**B+ tree** (PostgreSQL, MySQL InnoDB, most RDBMS indexes):
- Balanced tree, all data in leaves, leaves linked for range scans.
- Updates happen **in place** on the right page.

**LSM tree** (RocksDB, Cassandra, LevelDB, Kafka indexes):
- Writes go to an in-memory **memtable** (sorted, e.g., skip list).
- Memtable flushes to immutable **SSTables** on disk.
- Background **compaction** merges SSTables, removes overwrites and tombstones.

## 2. The rule / the why
- **B+ tree** = read-optimized. Predictable O(log n) reads; writes do random I/O (the right leaf).
- **LSM** = write-optimized. All writes are sequential (fast SSD/HDD). Reads may check multiple SSTables → slower without bloom filters.
- Pick by workload: write-heavy → LSM. Read-heavy + transactional → B+ tree.

## 3. Java-specific behavior
- Use JDBC over Postgres/MySQL → B+ tree underneath.
- Use RocksDB JNI binding for embedded LSM stores.
- Kafka uses its own append-only log (degenerate LSM) — only writes, never compacts in place (uses log compaction for keyed topics).

## 4. System design angle
- Cassandra/Scylla/DynamoDB internals = LSM → great for time-series, IoT, write floods.
- Postgres + B+ tree = great for OLTP with mixed reads/writes.
- Write amplification: LSM rewrites data N times during compaction. Read amplification: LSM may touch many files per read.
- Space amplification: LSM keeps overwritten versions until compaction.

## 5. Common mistakes / traps
- Choosing Cassandra for an OLTP workload because it "scales" — pay in read latency variability.
- Forgetting LSM compaction pauses — tune compaction strategy (size-tiered vs leveled).
- Building B+ tree indexes you never use → write amplification with no read benefit.

## 6. Revision checklist
- One-line trade-off: ______
- Why LSM writes are sequential: ______
- The bloom filter's role in LSM reads: ______
- Workload picks: write-heavy → ______, OLTP → ______
