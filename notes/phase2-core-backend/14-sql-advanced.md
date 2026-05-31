# SQL beyond basics

> Phase 2 · Tags: `databases` `sql`

## 1. The concept
Beyond `SELECT … WHERE`, you need:
- **Joins**: INNER, LEFT/RIGHT, FULL, CROSS, SELF, LATERAL.
- **Aggregations**: `GROUP BY`, `HAVING`, the difference vs `WHERE` (HAVING filters after aggregation).
- **Window functions**: `ROW_NUMBER() OVER (...)`, `RANK`, `LAG`, `LEAD`, running totals — aggregate without collapsing rows.
- **CTEs**: `WITH foo AS (...) SELECT ...` — readable composition; recursive CTEs for trees/graphs.
- **EXPLAIN ANALYZE**: shows the actual execution plan and timing.

## 2. The rule / the why
The DB is usually your performance bottleneck. Writing SQL that the planner can optimize (and reading plans when it can't) is a high-leverage skill.

## 3. Java-specific behavior
- Spring Data JPA `@Query` lets you drop down to JPQL or native SQL.
- jOOQ gives type-safe SQL DSL in Java — great escape hatch from JPA for complex queries.
- Use parameterized queries (JDBC `PreparedStatement`, JPA `:param`) — never string concatenation (SQL injection).

```sql
SELECT u.id, u.name,
       COUNT(o.id) AS orders,
       ROW_NUMBER() OVER (ORDER BY COUNT(o.id) DESC) AS rank
FROM users u LEFT JOIN orders o ON o.user_id = u.id
GROUP BY u.id, u.name;
```

## 4. System design angle
- Reading plans: look for `Seq Scan` on large tables (missing index?), nested loops with high row counts, sort spills to disk.
- Materialized views = pre-computed query result; refresh on schedule or trigger.
- Read replicas absorb heavy reads; route via connection URL or routing data source.

## 5. Common mistakes / traps
- `SELECT *` in production code — breaks when schema changes, fetches more than needed.
- Missing indexes on join keys.
- `WHERE function(col) = x` defeats indexes (use `WHERE col = inverse(x)` when possible).
- `OFFSET` deep pagination — gets slower linearly; use keyset/cursor pagination.
- String concatenation for queries → SQL injection.
- Implicit type casts on indexed columns (e.g., `WHERE varchar_col = 123`) skip the index.

## 6. Revision checklist
- HAVING vs WHERE in one line: ______
- One window function and what it does: ______
- How to read an `EXPLAIN ANALYZE` plan: ______
- Why deep OFFSET pagination is slow: ______
