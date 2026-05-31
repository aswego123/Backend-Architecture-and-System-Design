# JVM performance tuning

> Phase 5 · Tags: `java` `jvm` `performance`

## 1. The concept
Going past defaults: profile, find the bottleneck (CPU, GC, locks, allocation), apply a targeted fix.

Tools:
- **JFR** (Java Flight Recorder) — built-in, low-overhead profiler.
- **async-profiler** — flame graphs for CPU, allocation, locks.
- **JMC** (Mission Control) — JFR UI.
- **jstack**, **jcmd** — stack and ops.
- **GC logs** — `-Xlog:gc*`.

## 2. The rule / the why
Don't tune in the dark. Measure → hypothesize → change one thing → measure again. Most "tuning" is fixing allocation hotspots or query patterns, not JVM flags.

## 3. Java-specific behavior
Common wins:
- Reduce allocation in hot loops (reuse buffers, avoid autoboxing).
- Switch GC: G1 → ZGC for low pause; Parallel for throughput batch jobs.
- Right-size heap to the working set; oversize doesn't help, undersize causes constant GC.
- `-XX:+AlwaysPreTouch` for predictable startup memory.

## 4. System design angle
- A latency spike is usually GC pause, lock contention, or a slow downstream — flame graph tells you which.
- JIT warm-up matters for serverless and CI benchmarks — use AOT (GraalVM native) or warmup loops.
- CPU profiles often reveal more wins than JVM flags (e.g., logging overhead, JSON serialization).

## 5. Common mistakes / traps
- Copy-pasting tuning flags from Java 8 blogs on a Java 21 JVM.
- Benchmarking without warm-up → measuring the interpreter.
- "Optimizing" by avoiding lambdas / streams — measure first; modern JIT handles them fine.
- Premature optimization of code that isn't hot.

## 6. Revision checklist
- Two profiling tools: ______
- Why measure before tuning: ______
- One common allocation hotspot: ______
- Why JIT warm-up matters: ______
