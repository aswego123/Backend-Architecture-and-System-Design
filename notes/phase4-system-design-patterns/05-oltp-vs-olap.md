# OLTP vs OLAP; row vs column stores

> Phase 4 · Tags: `databases`

## 1. The concept
- **OLTP** (Online Transaction Processing): many small reads/writes per second, point queries by key. Examples: Postgres, MySQL, MongoDB.
- **OLAP** (Online Analytical Processing): few large queries scanning millions of rows, aggregations. Examples: BigQuery, Snowflake, ClickHouse, Redshift, DuckDB.

Storage layout:
- **Row store**: all columns of one row stored together. Optimal for "give me this whole record". OLTP default.
- **Column store**: each column stored separately. Optimal for "sum/average one column across millions of rows". OLAP default; also enables great compression (similar values together).

## 2. The rule / the why
OLTP and OLAP workloads have opposite access patterns. Trying to do both on one engine is painful — usually pipe OLTP data into a separate OLAP store.

## 3. Java-specific behavior
- JDBC works for both; query patterns differ.
- ORM is OLTP-only territory; for OLAP use raw SQL or a DSL like jOOQ.
- Use DuckDB (embedded analytics DB) inside Java for ad-hoc local analysis.

## 4. System design angle
- ETL/ELT pipeline: replicate OLTP → data warehouse (via CDC, Kafka Connect, Airbyte).
- HTAP (hybrid) systems (TiDB, SingleStore) attempt both — useful but rarely as good as specialized stores.
- Operational analytics (dashboards on live data) → ClickHouse-style real-time OLAP.

## 5. Common mistakes / traps
- Running heavy analytics on the OLTP DB → lock contention, slow user-facing reads.
- Choosing a column store for transactional workload → high per-row write cost.
- Building an analytics dashboard against the OLTP replica with `SELECT *` queries.
- Forgetting that analytics queries can be 1000× more expensive than OLTP — budget accordingly.

## 6. Revision checklist
- OLTP vs OLAP in one line: ______
- Why columnar layouts compress better: ______
- One way to bridge OLTP and OLAP: ______
- One DB for each: ______
