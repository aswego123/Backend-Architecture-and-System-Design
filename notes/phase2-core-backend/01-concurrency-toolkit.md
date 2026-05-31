# Java concurrency toolkit

> Phase 2 · Tags: `java` `concurrency`

## 1. The concept
The building blocks for safe concurrent code in Java:
- **Threads & executors**: `Thread`, `ExecutorService`, `ScheduledExecutorService`, virtual threads.
- **Locks**: `synchronized`, `ReentrantLock`, `ReadWriteLock`, `StampedLock`.
- **Atomics**: `AtomicInteger`, `AtomicReference`, `LongAdder` (better for high-contention counters).
- **Concurrent collections**: `ConcurrentHashMap`, `CopyOnWriteArrayList`, `BlockingQueue` family.
- **Coordinators**: `CountDownLatch`, `CyclicBarrier`, `Semaphore`, `Phaser`.

## 2. The rule / the why
Shared mutable state + concurrency = bugs. You either eliminate sharing (immutability, per-thread state), eliminate mutation (functional style), or coordinate via locks/atomics. Each tool exists for a specific access pattern; picking the wrong one tanks throughput.

## 3. Java-specific behavior
- Use `Executors.newFixedThreadPool(n)` for CPU-bound, `newVirtualThreadPerTaskExecutor()` for I/O-bound (Java 21+).
- Prefer `ReentrantLock` over `synchronized` when you need `tryLock`, fairness, or interruptible waits — *and* when running virtual threads (avoids pinning).
- `LongAdder` > `AtomicLong` when many threads update one counter (striped internally).
- `ConcurrentHashMap.compute` / `computeIfAbsent` for atomic read-modify-write.

```java
var map = new ConcurrentHashMap<String, Integer>();
map.compute("key", (k, v) -> v == null ? 1 : v + 1);  // atomic
```

## 4. System design angle
- Server frameworks (Tomcat, Netty) hand each request to a thread from a pool — pool sizing = capacity planning.
- Rate limiters and circuit breakers use atomics and time-windowed counters.
- Background workers use `ScheduledExecutorService` or Quartz; for distributed schedules you need a separate system.

## 5. Common mistakes / traps
- Creating an unbounded `Executors.newCachedThreadPool()` in production → thread explosion.
- Holding a lock across an I/O call → throughput collapse.
- Using `Collections.synchronizedMap` then iterating without holding the lock → CME.
- `volatile` for compound updates (`v++`) instead of atomics.
- Sharing `SimpleDateFormat` across threads — not thread-safe; use `DateTimeFormatter`.

## 6. Revision checklist
- One tool per category (lock, atomic, coordinator, collection): ______
- When `LongAdder` beats `AtomicLong`: ______
- Why `ReentrantLock` pairs better with virtual threads: ______
- The cached thread pool trap: ______
