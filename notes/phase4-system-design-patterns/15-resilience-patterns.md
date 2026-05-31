# Circuit breakers, bulkheads, retries (Resilience4j)

> Phase 4 · Tags: `reliability` `java`

## 1. The concept
Patterns to keep one failing dependency from taking down the whole app:

- **Timeout**: cap waiting time.
- **Retry**: try again with backoff + jitter.
- **Circuit breaker**: when failure rate exceeds threshold, "open" the circuit — fail fast without calling the dep. After a wait, "half-open" to probe recovery.
- **Bulkhead**: isolate resources per dependency (separate thread pools / semaphores) so one slow dep can't starve others.
- **Rate limiter**: cap outbound rate to a dependency.
- **Fallback**: degraded but useful response when the dep is down.

## 2. The rule / the why
Without these, a single slow downstream → all your threads blocked → cascading failure across upstream services.

## 3. Java-specific behavior
**Resilience4j** is the de facto Java library. Composable, lightweight, no external deps.

```java
CircuitBreaker cb = CircuitBreaker.ofDefaults("payments");
TimeLimiter tl = TimeLimiter.of(Duration.ofMillis(500));
Bulkhead bh = Bulkhead.of("payments", BulkheadConfig.custom().maxConcurrentCalls(20).build());

Supplier<Receipt> call = () -> client.charge(req);
Supplier<Receipt> protected_ = Decorators.ofSupplier(call)
    .withCircuitBreaker(cb)
    .withBulkhead(bh)
    .withFallback(List.of(Exception.class), ex -> Receipt.queuedForRetry())
    .decorate();
```

- Spring Cloud Circuit Breaker provides annotations (`@CircuitBreaker`, `@Retry`).

## 4. System design angle
- Per-dependency bulkheads prevent shared-resource starvation.
- Circuit breaker state should be visible (metrics, dashboards).
- Fallbacks should be idempotent and not call the same failing dep.
- Stack: timeout → retry → circuit breaker → bulkhead → fallback.

## 5. Common mistakes / traps
- Retry without circuit breaker → amplifies outage.
- Fallback that calls another flaky service → just moves the problem.
- Global thread pool for all outbound calls → no isolation.
- Circuit breaker with too-sensitive threshold → constantly tripping.
- No fallback → user sees errors for problems you could degrade gracefully.

## 6. Revision checklist
- Circuit breaker states: ______
- Why bulkheads matter: ______
- The retry-without-CB amplification: ______
- One Resilience4j primitive: ______
