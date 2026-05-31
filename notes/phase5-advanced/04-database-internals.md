# Database internals

> Phase 5 · Tags: `databases`

## 1. The concept
The pieces inside a relational DB:
- **Storage engine**: how rows live on disk (heap, clustered index, LSM).
- **WAL (Write-Ahead Log)**: every change is written to a log before the data file → enables crash recovery and replication.
- **Buffer pool**: in-memory cache of pages from disk.
- **MVCC**: row versioning for snapshot isolation; needs garbage collection of old versions.
- **Query planner / optimizer**: cost-based, uses table statistics to pick join order, index choice, etc.
- **Execution engine**: pulls or pushes tuples through operators (nested loop, hash join, sort, agg).
- **Lock manager**: row, page, table locks for write conflicts.

## 2. The rule / the why
Knowing the internals turns mysterious behavior into predictable behavior — slow queries, lock waits, replica lag, vacuum behavior.

## 3. Java-specific behavior
- `EXPLAIN ANALYZE` is the bridge from app to internals.
- Postgres extensions like `pg_stat_statements`, `auto_explain` reveal the actual cost of your JDBC queries.

## 4. System design angle
- WAL shipping is how replication and PITR (point-in-time recovery) work.
- Most "DB slow" issues come from missing indexes, bad plan choices, or buffer pool pressure — not the engine itself.
- Read replicas have lag because they replay WAL — quantify it as a metric.

## 5. Common mistakes / traps
- Trusting query plans without re-running ANALYZE after big data changes.
- Long-running transactions blocking vacuum → bloat in MVCC systems.
- Ignoring buffer pool hit ratio in monitoring.

## 6. Revision checklist
- WAL purpose: ______
- Buffer pool role: ______
- Two operators a query plan might use: ______
- One tool to inspect actual queries: ______
