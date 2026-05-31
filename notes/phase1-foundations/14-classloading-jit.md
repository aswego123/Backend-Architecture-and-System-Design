# Class loading, bytecode, JIT

> Phase 1 · Tags: `java` `jvm`

## 1. The concept
- **Class loading**: `.class` files are loaded lazily by `ClassLoader`s on first use.
- **Bytecode**: stack-based instructions in `.class` files (`javap -c` to inspect).
- **JIT (Just-In-Time)**: the JVM starts interpreting bytecode, then HotSpot compiles "hot" methods to native code in tiers: C1 (fast compile, less optimized) → C2 (slower compile, fully optimized). GraalVM is an alternative compiler.
- **Tiered compilation**: default since Java 8 — best of both.

## 2. The rule / the why
- Lazy loading keeps startup fast.
- JIT lets Java match or beat ahead-of-time languages because it has **runtime profile data** (which branches are taken, which classes monomorphic) that static compilers don't.
- The cost: warm-up. First few thousand calls are slow until JIT kicks in.

## 3. Java-specific behavior
- Class loader hierarchy: Bootstrap → Platform → App → custom. Children delegate up first (so you can't override `String`).
- Inspect JIT: `-XX:+PrintCompilation`, JITWatch, async-profiler.
- AOT options: GraalVM Native Image compiles ahead of time → near-instant startup, no warm-up, but loses some peak throughput and dynamic features (reflection needs config).

## 4. System design angle
- Cold start matters for serverless (Lambda) → AOT/native image wins.
- Long-running services benefit from JIT's runtime profiling.
- Class loader leaks are the #1 reason for "PermGen/Metaspace OOM" in app servers redeploying webapps.

## 5. Common mistakes / traps
- Benchmarking the first N runs — you measured the interpreter, not the JIT. Use JMH.
- Heavy reflection or dynamic class generation can defeat JIT optimizations.
- Class loader leaks: a redeployed webapp keeps a reference (often via thread, ThreadLocal, JDBC driver) → old version never GC'd.
- Assuming startup time is fixed — it's mostly class loading; tools like CDS (`-Xshare:auto`) and AppCDS help.

## 6. Revision checklist
- Why JIT can beat AOT for long-running apps: ______
- What "warm-up" means: ______
- One cause of a class loader leak: ______
- When to pick GraalVM native image: ______
