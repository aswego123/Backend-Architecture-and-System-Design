# MVCC (Multi-Version Concurrency Control)

> Phase 2 · Tags: `databases` `postgres`

## 1. The concept
Each row update creates a **new version** of the row, tagged with the transaction ID that created it. Readers see the version visible at their transaction's start; writers don't block readers, readers don't block writers.

Postgres example: every row has hidden `xmin` (creator txn) and `xmax` (deleter txn) columns. A snapshot defines which versions are visible.

## 2. The rule / the why
Locking-based concurrency (older systems) makes reads block writes and vice versa → terrible throughput. MVCC gives high concurrency with consistent snapshots — at the cost of needing to clean up old versions later (vacuum/compaction).

## 3. Java-specific behavior
- Transparent to JDBC/JPA developers.
- What you'll see: occasional "could not serialize access due to concurrent update" (Serializable Snapshot Isolation conflicts) → catch and retry.
- Long-lived JDBC transactions in Postgres hold back vacuum → table and index bloat.

## 4. System design angle
- Postgres, Oracle, SQL Server (snapshot isolation), MySQL InnoDB all use MVCC variants.
- Time-travel queries (`AS OF SYSTEM TIME`) build on MVCC (CockroachDB, BigQuery).
- Repeatable Read in Postgres = snapshot isolation, not the SQL-standard RR.

## 5. Common mistakes / traps
- Idle-in-transaction connections holding snapshots → vacuum can't reclaim → bloat → slow queries.
- High update rate on a small table without aggressive autovacuum → bloat spiral.
- `SELECT COUNT(*)` in Postgres is slow on large tables because it must check visibility of each row.
- Misreading "snapshot" — it's at transaction start (or first statement in Read Committed).

## 6. Revision checklist
- MVCC's core trick: ______
- Why long transactions are dangerous in MVCC systems: ______
- One DB that uses MVCC: ______
- Why `COUNT(*)` is slow on Postgres: ______
