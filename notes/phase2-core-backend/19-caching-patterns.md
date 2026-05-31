# Cache patterns & eviction

> Phase 2 · Tags: `caching`

## 1. The concept
Caching = store recently/likely-needed data closer to the consumer to avoid recomputation or origin hits.

**Patterns**:
- **Cache-aside (lazy loading)**: app checks cache; on miss, fetches from DB and writes to cache. Most common.
- **Read-through**: cache is in front; cache itself loads from DB on miss.
- **Write-through**: write to cache and DB synchronously.
- **Write-behind (write-back)**: write to cache, flush to DB async. Fast writes; risk of data loss on crash.
- **Refresh-ahead**: proactively refresh popular entries before TTL.

**Eviction policies**: LRU (Least Recently Used), LFU (Least Frequently Used), FIFO, ARC, TinyLFU (Caffeine).

## 2. The rule / the why
- Reduces latency and load on origin.
- Adds a consistency problem: cache can be stale.
- Right pattern depends on read/write ratio and consistency tolerance.

## 3. Java-specific behavior
- **Caffeine**: high-performance in-process cache (TinyLFU). Use for per-instance data.
- **Spring Cache** abstraction: `@Cacheable`, `@CacheEvict`, `@CachePut` — backed by Caffeine, Redis, etc.
- **Redis**: shared cache across instances; supports TTL natively.

```java
@Cacheable(value = "users", key = "#id")
public User get(Long id) { return repo.findById(id).orElseThrow(); }

@CacheEvict(value = "users", key = "#user.id")
public void update(User user) { repo.save(user); }
```

## 4. System design angle
- **Cache stampede**: TTL expires under load → 1000 requests miss simultaneously → all hit DB. Fixes: request coalescing (single-flight), probabilistic early expiration, jittered TTL, mutex-per-key.
- **Negative caching**: cache "not found" results so missing keys don't pound the DB.
- **TTL vs explicit invalidation**: TTL is simple but allows staleness; invalidation is fresh but hard in distributed caches.
- L1 (per-instance Caffeine) + L2 (shared Redis) is a common pattern.

## 5. Common mistakes / traps
- Cache invalidation is one of the two hardest things in CS.
- Caching without a TTL → ever-growing memory + stale data forever.
- Caching the wrong layer (cache before validation → caches errors).
- Cache key collisions across tenants/versions — namespace your keys.
- Treating a cache as a database (no persistence guarantees by default).

## 6. Revision checklist
- Cache-aside vs read-through: ______
- Name two eviction policies: ______
- One stampede mitigation: ______
- Why negative caching matters: ______
