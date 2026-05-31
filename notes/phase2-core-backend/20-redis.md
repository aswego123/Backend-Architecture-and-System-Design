# Redis as a building block

> Phase 2 · Tags: `caching` `redis`

## 1. The concept
Redis = in-memory data structure server. Single-threaded command execution (no internal locks needed), microsecond latency on a LAN.

Data types: `STRING`, `HASH`, `LIST`, `SET`, `ZSET` (sorted set), `STREAM`, `HYPERLOGLOG`, `BITMAP`, `GEO`.

## 2. The rule / the why
Redis isn't just a cache. Pick the right data structure and it replaces a custom service:
- Rate limiter → `INCR` + `EXPIRE`.
- Leaderboard → `ZSET` + `ZRANGEBYSCORE`.
- Distributed lock → `SET key value NX PX 10000` (with Redlock for HA — but be careful, see fencing).
- Queue → `LIST` + `BLPOP`, or `STREAM` for durable consumer groups.
- Session store → `HASH` with TTL.
- Pub/sub → `PUBLISH`/`SUBSCRIBE` (fire-and-forget) or `STREAM` (persistent).

## 3. Java-specific behavior
- **Lettuce** (default in Spring Boot): netty-based, async/reactive-friendly.
- **Jedis**: simpler, blocking, thread-unsafe by default (pool it).
- Spring Data Redis: `RedisTemplate`, `StringRedisTemplate`, repositories.
- Spring Cache + Redis = `@Cacheable` backed by Redis with TTLs.

## 4. System design angle
- **Persistence**: RDB snapshots (periodic, fast) + AOF (append-only log, durable). Treat as a cache by default; configure persistence if it's a source of truth.
- **Cluster**: hash-slot sharding (16384 slots). Multi-key ops require keys to hash to the same slot → use `{hashtag}` braces.
- **Replication**: async by default → possible data loss on failover. WAIT command + reasonable replica acks help.
- **Eviction**: `maxmemory` + policy (`allkeys-lru`, `volatile-lru`, etc.) when memory caps.

## 5. Common mistakes / traps
- `KEYS *` in production → blocks the server. Use `SCAN`.
- Storing huge values (multi-MB) → blocks Redis (single-threaded).
- Distributed lock without **fencing token** → split-brain can corrupt protected resource.
- Treating async replication as synchronous → lost writes on failover.
- Forgetting TTLs → memory grows unbounded.
- Storing serialized Java objects with default JDK serialization — slow and fragile; use JSON or Kryo.

## 6. Revision checklist
- Three data types beyond STRING and one use case each: ______
- Why `KEYS` is dangerous: ______
- Cluster multi-key restriction: ______
- The distributed-lock fencing concern: ______
