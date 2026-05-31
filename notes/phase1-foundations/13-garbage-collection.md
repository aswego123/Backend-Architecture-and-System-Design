# Garbage collection (G1, ZGC, Shenandoah)

> Phase 1 · Tags: `java` `jvm` `performance`

## 1. The concept
GC frees memory automatically by finding objects no longer **reachable** from GC roots (thread stacks, statics, JNI). The cost is occasional pauses to do the work safely.

Generational hypothesis: most objects die young → split heap into Young (cheap, frequent collection) and Old (expensive, rare).

Modern collectors:
- **G1** (default since Java 9): region-based, mostly-concurrent, aims for predictable pause goals. Sweet spot 4–32 GB.
- **ZGC** (production since Java 15): concurrent, scalable to TB heaps, sub-ms pauses, slightly more CPU.
- **Shenandoah** (Red Hat): similar goals to ZGC, concurrent compaction.
- **Parallel GC**: high throughput, stop-the-world; for batch jobs.
- **Serial GC**: tiny services, single thread.

## 2. The rule / the why
Without GC: manual `free()` bugs (use-after-free, leaks) dominate. With GC: you trade some CPU and occasional pause latency for safety.

Choosing a collector is a latency-vs-throughput-vs-footprint trade-off, *not* "newer is always better".

## 3. Java-specific behavior
- Choose: `-XX:+UseG1GC`, `-XX:+UseZGC`, `-XX:+UseShenandoahGC`, `-XX:+UseParallelGC`.
- Set heap: `-Xms`, `-Xmx` (equal in containers to avoid resize pauses).
- Tune target pause: `-XX:MaxGCPauseMillis=200` (G1 honors as a goal, not a guarantee).
- Always log GC: `-Xlog:gc*:file=gc.log:time,uptime,level,tags`.

## 4. System design angle
- Low-latency trading systems → ZGC/Shenandoah or off-heap memory.
- Batch / analytics → Parallel GC for throughput.
- Microservices in containers → G1 with `MaxRAMPercentage=75`.
- A tail-latency p99 spike is often a GC pause — first place to look.

## 5. Common mistakes / traps
- Manual `System.gc()` calls — at best ignored, at worst forces a full GC.
- Heap too small → constant GC. Heap too big → fewer but longer pauses.
- Ignoring direct/off-heap memory in container limits → OOMKilled despite low heap usage.
- Reading "GC tuning" blogs from Java 8 — most advice is obsolete on G1/ZGC.

## 6. Revision checklist
- Generational hypothesis in one line: ______
- When to pick ZGC over G1: ______
- One JVM flag to set in every prod app: ______
- The relation between GC and p99 latency: ______
