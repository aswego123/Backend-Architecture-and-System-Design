# Backpressure & flow control

> Phase 3 · Tags: `distributed` `reliability`

## 1. The concept
Backpressure = a downstream consumer signaling upstream to **slow down** when it can't keep up. Without it, queues grow, latency explodes, and eventually OOM.

Mechanisms:
- **Bounded queue**: producer blocks (or fails) when full.
- **Credit-based**: consumer grants N credits; producer sends at most N.
- **Reactive Streams** (`request(n)`): subscriber pulls demand from publisher.
- **Load shedding**: drop or reject excess requests at the edge.

## 2. The rule / the why
The internet doesn't have natural backpressure → senders can overwhelm receivers. Building it in is the difference between graceful degradation and cascading failure.

## 3. Java-specific behavior
- `BlockingQueue` family with bounded capacity for producer-consumer.
- Reactive Streams (`Flow.Publisher`, Reactor, RxJava): `request(n)` baked in.
- `Semaphore` to bound concurrent in-flight work.
- Kafka consumer: poll loop is implicit backpressure (don't poll if you can't process).

```java
// Bounded executor with caller-runs rejection: producer blocks on overload
new ThreadPoolExecutor(8, 8, 0L, TimeUnit.SECONDS,
    new ArrayBlockingQueue<>(100),
    new ThreadPoolExecutor.CallerRunsPolicy());
```

## 4. System design angle
- Every queue in your system needs a bound. Unbounded = eventual OOM under load.
- Reject early (429 Too Many Requests at the gateway) instead of accepting and timing out.
- Drop low-priority work first (shed tail traffic, keep critical paths).
- Backpressure must propagate end-to-end — front-of-stack drops are useless if mid-stack still accepts everything.

## 5. Common mistakes / traps
- `Executors.newCachedThreadPool()` or `newFixedThreadPool(unbounded queue)` → unbounded growth.
- Reactive Streams with `Flux.onBackpressureBuffer()` and no cap → still unbounded.
- Returning 200 OK to "ingested" then dropping → silent data loss.
- Backpressure at one hop but not the next → just moves the bottleneck.

## 6. Revision checklist
- Define backpressure: ______
- Why every queue needs a bound: ______
- One JVM API that supports backpressure: ______
- Load shedding vs queueing: ______
