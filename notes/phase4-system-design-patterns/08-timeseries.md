# Time-series & analytics stores

> Phase 4 · Tags: `databases`

## 1. The concept
**Time-series databases (TSDB)** are optimized for `(timestamp, metric, tags) → value` write-heavy workloads with retention/downsampling.

Examples: Prometheus (metrics, single-node), InfluxDB, TimescaleDB (Postgres extension), VictoriaMetrics, ClickHouse (general OLAP that excels at TS).

Common features:
- Columnar storage + heavy compression (delta-of-delta, Gorilla encoding).
- Time-based partitioning (chunks per hour/day).
- Downsampling (rollups): keep 1s resolution for a day, 1m for a month, 1h for a year.
- Retention policies that drop old chunks cheaply.

## 2. The rule / the why
- Metrics and event data write at extreme rates and are queried mostly by time range.
- Generic RDB can store them but indexes/B+ trees become a bottleneck. TSDBs use columnar + time partitioning for 10–100× the throughput.

## 3. Java-specific behavior
- Micrometer integrates with Prometheus, InfluxDB, etc., via registries.
- TimescaleDB is just Postgres — use existing JDBC driver. Create a hypertable with a one-line SQL command.
- ClickHouse JDBC driver works for OLAP queries.

## 4. System design angle
- Monitoring stack: Prometheus + Grafana for metrics; Loki for logs; Tempo for traces.
- IoT/telemetry: TimescaleDB or InfluxDB ingest; ClickHouse for ad-hoc analysis.
- Cardinality is the killer dimension — high tag cardinality (millions of unique series) crushes most TSDBs.

## 5. Common mistakes / traps
- Tagging by user ID → unbounded cardinality.
- Querying "last 30 days at 1s resolution" when you should query a downsampled rollup.
- Using Postgres without TimescaleDB for high-volume TS → index bloat.
- Mixing TS data with OLTP in the same DB → vacuum and index contention.

## 6. Revision checklist
- Why TSDBs use columnar storage: ______
- What downsampling is: ______
- The cardinality trap: ______
- One TSDB and one general OLAP DB: ______
