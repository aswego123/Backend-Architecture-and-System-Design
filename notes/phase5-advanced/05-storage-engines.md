# Storage engine design

> Phase 5 · Tags: `databases` `storage`

## 1. The concept
Two dominant designs (revisited from Phase 1, deeper here):
- **B+ tree**: read-optimized, in-place updates. Postgres, InnoDB, most RDBMS.
- **LSM tree**: write-optimized via memtable + sorted runs + compaction. Cassandra, RocksDB, LevelDB.

Key trade-offs:
- **Write amplification**: bytes written to disk per logical write.
- **Read amplification**: extra reads per logical read.
- **Space amplification**: extra space relative to live data.

LSM has high write amp from compaction, multi-level reads (mitigated by bloom filters), and temporary space amp.

B+ tree has page-level write amp (whole page rewritten for a row update), lower read amp (one tree walk).

## 2. The rule / the why
Pick the engine by workload shape, not vendor reputation. LSM dominates write-heavy, append-mostly data (logs, events, time-series). B+ tree dominates mixed OLTP.

## 3. Java-specific behavior
- RocksDB JNI is the go-to embedded LSM for Java apps needing high-throughput KV.
- MapDB for embedded B+ tree style.

## 4. System design angle
- Compaction strategies (size-tiered vs leveled) trade write amp vs space amp.
- Bloom filters in front of SSTables are mandatory for reasonable reads.
- Newer hybrids: BW-tree (in-memory + log), Fractal trees (TokuDB).

## 5. Common mistakes / traps
- Choosing LSM for OLTP without tuning compaction → latency spikes.
- B+ tree under sustained writes without batching → IOPS bottleneck.
- Ignoring write amp until SSD wear becomes the surprise cost.

## 6. Revision checklist
- Three amplifications: ______
- One LSM and one B+ engine: ______
- Why bloom filters matter for LSM: ______
