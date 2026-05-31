# Processes, threads, virtual threads

> Phase 1 · Tags: `os` `concurrency` `java`

## 1. The concept
- **Process**: an OS-level isolated execution unit with its own memory space, file descriptors, PID.
- **Thread**: an execution path *inside* a process; threads in one process share memory and FDs.
- **Virtual thread (Java)**: a user-space thread scheduled by the JVM onto a small pool of OS threads. Cheap (KBs, not MBs).

Mental model: process = apartment, thread = roommate sharing the apartment, virtual thread = a task one roommate is multitasking.

## 2. The rule / the why
- Processes give **isolation** (a crash doesn't take down siblings) but are expensive to create and context-switch.
- Threads give **shared memory** for fast communication, but require synchronization and risk races.
- Virtual threads exist because OS threads are too heavy for the "one thread per request" model at scale (10k+ concurrent requests).

Without threads: you can't use multiple cores. Without virtual threads: blocking I/O forces you into callback hell (reactive).

## 3. Java-specific behavior
- `Thread` wraps an OS thread (~1 MB stack). Create with `new Thread(runnable).start()`.
- `ExecutorService` pools threads so you don't create one per task.
- Since Java 21: `Thread.ofVirtual().start(runnable)` or `Executors.newVirtualThreadPerTaskExecutor()`. Virtual threads "unmount" the carrier OS thread when they block on I/O.
- `ThreadLocal` survives across virtual threads but should be avoided in favor of `ScopedValue` (preview).

```java
try (var exec = Executors.newVirtualThreadPerTaskExecutor()) {
    IntStream.range(0, 10_000).forEach(i ->
        exec.submit(() -> { Thread.sleep(1000); return i; }));
} // ~1 second, not 10,000 seconds
```

## 4. System design angle
- Server concurrency model dictates throughput shape: blocking + many threads (Tomcat classic), event loop + few threads (Node, Netty), virtual threads (modern JVM).
- Process-per-request (CGI) is dead for performance; thread-per-request is back in vogue thanks to virtual threads.
- Microservices isolate failures by **process** boundary; threads alone aren't enough.

## 5. Common mistakes / traps
- Using `synchronized` blocks inside virtual threads that hold the carrier thread → pinning, kills the benefit. Prefer `ReentrantLock`.
- Sizing thread pools by guesswork. For CPU-bound: ≈ #cores. For I/O-bound (platform threads): `cores × (1 + waitTime/computeTime)`.
- Assuming threads "run in parallel" — only on multi-core, and only if not blocked on a shared lock.
- Sharing mutable state across threads without `volatile`, `Atomic*`, or a lock.

## 6. Revision checklist
- Difference between process and thread in one line: ______
- Why virtual threads are not just "smaller threads": ______
- One scenario where you'd still use platform threads: ______
- The pinning trap: ______
