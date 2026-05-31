# Connection pooling (HikariCP)

> Phase 2 · Tags: `databases` `java` `performance`

## 1. The concept
A **connection pool** keeps a set of DB connections open and hands them out on demand. Opening a TCP+TLS+auth handshake per query would dominate latency.

**HikariCP**: the fast, default pool in Spring Boot.

## 2. The rule / the why
- Connections are expensive to create (10–100 ms).
- DBs limit concurrent connections (Postgres default ~100). Each connection consumes RAM on the DB.
- Pool size = the upper bound on concurrent DB work, not on app concurrency.

## 3. Java-specific behavior
Spring Boot `application.yml`:
```yaml
spring.datasource.hikari:
  maximum-pool-size: 10
  minimum-idle: 10
  connection-timeout: 2000   # ms to wait for a connection
  max-lifetime: 1800000       # 30 min, less than DB's idle timeout
  leak-detection-threshold: 30000
```

## 4. System design angle
- Pool sizing heuristic (HikariCP wiki, derived from Little's Law):
  `pool_size ≈ ((core_count × 2) + effective_spindle_count)`
  Most apps land near 10–30 per service instance.
- Total connections across all app instances must stay below DB max — use a connection proxy (PgBouncer) for many small services.
- A pool that's "too big" can be *worse* than "too small" — overloads the DB, increases lock waits.

## 5. Common mistakes / traps
- Setting `maximum-pool-size` to a huge number "for safety" → DB tipover.
- Holding a connection across a slow external call (e.g., REST call) → pool exhaustion.
- Not setting `max-lifetime` < DB's idle timeout → connections silently die, queries fail.
- No `leak-detection-threshold` → missed `close()` in old code drains the pool.
- Mixing transactional and non-transactional work in one method → connection held longer than needed.

## 6. Revision checklist
- Why pooling exists: ______
- Why bigger isn't better: ______
- One config you must align with DB settings: ______
- The "connection held across remote call" anti-pattern: ______
