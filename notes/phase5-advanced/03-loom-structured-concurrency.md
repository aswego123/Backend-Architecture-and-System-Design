# Project Loom & structured concurrency

> Phase 5 · Tags: `java` `concurrency`

## 1. The concept
- **Virtual threads** (Java 21): JVM-scheduled threads, cheap (KBs), millions per JVM.
- **Structured concurrency** (preview): a parent scope owns child tasks; tasks complete or cancel together. Replaces unstructured `ExecutorService` "shoot and forget".
- **Scoped values** (preview): immutable per-thread values that work nicely with virtual threads (replaces `ThreadLocal` for most uses).

## 2. The rule / the why
Loom makes "thread per request" viable again at scale, removing the need for reactive-style code in most apps. Structured concurrency gives async code the same scoping guarantees you have for try/catch with sync code.

## 3. Java-specific behavior

```java
try (var scope = new StructuredTaskScope.ShutdownOnFailure()) {
    Subtask<User> u = scope.fork(() -> fetchUser(id));
    Subtask<Prefs> p = scope.fork(() -> fetchPrefs(id));
    scope.join().throwIfFailed();
    return new UserView(u.get(), p.get());
}
```

- Spring Boot 3.2+: `spring.threads.virtual.enabled=true` flips Tomcat to virtual threads per request.
- Watch `synchronized` blocks — they pin the carrier thread. Prefer `ReentrantLock`.

## 4. System design angle
- Most "we need reactive" cases evaporate with virtual threads.
- DB drivers + libraries that lock or use ThreadLocal heavily may misbehave — test the hot path under load.
- Structured concurrency makes timeouts/cancellation reliable across fanouts.

## 5. Common mistakes / traps
- Massive ThreadLocal usage breaks virtual threads' memory advantage.
- `synchronized` around I/O pins carrier threads → throughput cliff.
- Treating virtual threads as a perf silver bullet — they help I/O-bound workloads, not CPU-bound.

## 6. Revision checklist
- Virtual thread vs platform thread: ______
- What structured concurrency adds: ______
- One pinning cause: ______
- When virtual threads don't help: ______
