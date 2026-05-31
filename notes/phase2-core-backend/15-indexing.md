# Indexing

> Phase 2 · Tags: `databases`

## 1. The concept
An **index** is a separate data structure (usually a B+ tree) that maps column values → row locations, so the DB can find rows without scanning the table.

Variants:
- **Composite index** `(a, b, c)`: usable for queries filtering on `a`, `a,b`, or `a,b,c` — leftmost prefix rule.
- **Covering index**: includes all columns the query reads → no table lookup needed (index-only scan).
- **Partial index**: built only on rows matching a `WHERE` clause (Postgres).
- **Hash index**: O(1) equality lookup, no range scans.
- **GIN / GiST** (Postgres): for full-text, JSONB, arrays.

## 2. The rule / the why
Without an index, the DB does a sequential scan → O(N). With the right index → O(log N).

But indexes cost: extra write amplification (each insert/update modifies indexes) and extra disk space. Don't index everything.

## 3. Java-specific behavior
- ORM (JPA) doesn't think about indexes for you. Declare them in DDL / migrations (Flyway/Liquibase).
- `@Index` annotation in JPA only generates DDL when Hibernate creates the schema — don't rely on it in prod.
- Use `EXPLAIN ANALYZE` (Postgres) or `EXPLAIN FORMAT=JSON` (MySQL) to verify index use.

## 4. System design angle
- Read-heavy workloads → more indexes pay off.
- Write-heavy / append-only (logs, events) → fewer indexes; consider LSM-based stores.
- Foreign keys should usually be indexed (Postgres doesn't do it automatically).
- Composite index ordering: match query patterns, not cardinality alone.

## 5. Common mistakes / traps
- Indexing every column "just in case" → write performance dies.
- Wrong column order in composite index → query can't use it.
- Indexing low-cardinality columns alone (`status` with 3 values) — usually no benefit; pair with another column.
- Functions/casts on the indexed column kill index use.
- Forgetting to ANALYZE/VACUUM (Postgres) — planner uses stale stats.

## 6. Revision checklist
- Leftmost prefix rule in one line: ______
- What "covering" means: ______
- Write-vs-read trade-off of indexes: ______
- One way to verify an index is used: ______
