# Failure detection, timeouts, retries

> Phase 3 · Tags: `distributed` `reliability`

## 1. The concept
- **Timeout**: cap on how long you wait. Lower bound on error detection; upper bound on resource hold.
- **Retry**: try again on transient failure.
- **Backoff**: wait longer between retries (exponential).
- **Jitter**: randomize backoff to avoid synchronized retries (the thundering herd).
- **Circuit breaker**: stop calling a failing dependency for a while; fail fast.
- **Health checks**: liveness (am I alive?) vs readiness (am I ready to serve?).

## 2. The rule / the why
Without timeouts, one slow downstream hangs you. Without retries, transient blips become outages. Without backoff+jitter, retries amplify outages into stampedes.

## 3. Java-specific behavior
- Resilience4j: `Retry`, `CircuitBreaker`, `RateLimiter`, `Bulkhead`, `TimeLimiter` — composable.
- Spring Cloud Circuit Breaker abstracts over Resilience4j / others.
- gRPC and HTTP clients (`HttpClient`, Feign, RestClient) accept per-request timeouts — set them.

```java
Retry retry = Retry.of("payments", RetryConfig.custom()
    .maxAttempts(3)
    .waitDuration(Duration.ofMillis(200))
    .retryExceptions(IOException.class)
    .build());
Supplier<Receipt> decorated = Retry.decorateSupplier(retry, () -> client.charge(req));
```

## 4. System design angle
- **Three timeouts**: connect, read, total. Set all three.
- Retry budget: cap total retries across the fleet to avoid amplification (`X% of original traffic`).
- Hedged requests: send to two replicas, take the first response; lowers tail latency at the cost of doubled load.
- Circuit breaker states: closed → open → half-open → closed.

## 5. Common mistakes / traps
- Retrying non-idempotent operations → duplicate writes.
- Infinite retries → resource exhaustion.
- Same backoff on every caller → synchronized retry storms (need jitter).
- Liveness probe that hits the DB → outage in DB takes down all pods.
- Timeouts longer than the upstream timeout → caller already gave up while you're still working.

## 6. Revision checklist
- Three timeouts to set: ______
- Why jitter matters: ______
- Liveness vs readiness: ______
- Retry + non-idempotent = ______
