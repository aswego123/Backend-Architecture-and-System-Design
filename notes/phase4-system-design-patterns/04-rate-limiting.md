# Rate limiting algorithms

> Phase 4 · Tags: `infra` `reliability`

## 1. The concept
Rate limiting caps how many requests a client can make in a time window.

Algorithms:
- **Fixed window**: count per minute, reset on the minute. Cheap, but allows 2x burst at window boundary.
- **Sliding window log**: store timestamps of each request; count those in the window. Accurate, expensive memory.
- **Sliding window counter**: weighted average of current + previous window. Good approximation.
- **Token bucket**: bucket holds N tokens, refills at rate R. Each request consumes a token. Allows bursts up to N.
- **Leaky bucket**: requests enter a queue draining at fixed rate. Smooths bursts to constant rate.

## 2. The rule / the why
Protects against abuse, runaway clients, and accidental DOS. Also enforces fair use across tenants and protects downstream services.

## 3. Java-specific behavior
- Bucket4j: token bucket implementation, works in-memory or Redis-backed.
- Resilience4j `RateLimiter`: per-instance.
- Spring Cloud Gateway `RequestRateLimiter` + `RedisRateLimiter`: distributed.

```java
Bucket bucket = Bucket.builder()
    .addLimit(Bandwidth.simple(100, Duration.ofMinutes(1)))
    .build();
if (!bucket.tryConsume(1)) throw new TooManyRequestsException();
```

## 4. System design angle
- **Where to enforce**: edge (gateway), per-service, per-DB. Each layer protects what's behind it.
- **Per-key choice**: IP (basic), API key (per customer), user ID (per session). Combine.
- **Distributed limits**: keep counters in Redis with atomic `INCR` + `EXPIRE`.
- **Return 429 + `Retry-After` header** so clients back off correctly.

## 5. Common mistakes / traps
- Fixed window's 2x burst trap (1 req at 11:59:59 + 1 at 12:00:00 = 2 in a "1/min" limit).
- Per-instance limits with N instances → effective limit = N × intended.
- Rate-limit by IP only → NAT'd offices share a quota.
- Forgetting to return `Retry-After` → clients retry immediately, amplifying the problem.

## 6. Revision checklist
- Name two algorithms and one trade-off each: ______
- Why token bucket allows bursts: ______
- How to make a limit truly distributed: ______
- The fixed-window edge bug: ______
