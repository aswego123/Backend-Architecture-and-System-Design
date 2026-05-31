# JVM flags you actually need

> Phase 2 · Tags: `java` `jvm` `ops`

## 1. The concept
The JVM has hundreds of flags. In practice you only need a small set in production.

## 2. The rule / the why
Defaults are tuned for laptops, not containerized servers. A few flags eliminate 90% of common production issues (OOMKilled, no heap dumps, no GC logs).

## 3. Java-specific behavior (the essential set)

```bash
# Heap sizing — in a container, use percentage so it tracks cgroup limits
-XX:InitialRAMPercentage=75 -XX:MaxRAMPercentage=75

# GC choice (G1 is default; pick ZGC for low-latency)
-XX:+UseG1GC
# or: -XX:+UseZGC -XX:+ZGenerational

# Heap dump on OOM — invaluable for postmortem
-XX:+HeapDumpOnOutOfMemoryError
-XX:HeapDumpPath=/var/log/app/heap.hprof

# GC logging (rotated)
-Xlog:gc*:file=/var/log/app/gc.log:time,uptime,level,tags:filecount=5,filesize=20M

# Exit on OOM so the orchestrator can restart cleanly
-XX:+ExitOnOutOfMemoryError

# Container awareness (on by default since JDK 10+, but be explicit)
-XX:+UseContainerSupport
```

## 4. System design angle
- A JVM that silently leaks → OOMKilled by Kubernetes → no heap dump → no root cause. The flags above prevent that.
- `MaxRAMPercentage` of 75 leaves headroom for direct buffers, threads, Metaspace, and the kernel.
- For burstable workloads, prefer fixed `-Xms = -Xmx` to avoid resize pauses.

## 5. Common mistakes / traps
- Setting `-Xmx` equal to container memory → OOMKilled before heap fills.
- No heap dump path → can't diagnose OOM after the fact.
- Forgetting GC logs → tail-latency mysteries.
- Copy-pasting Java 8 tuning flags (e.g., `-XX:CMSInitiatingOccupancyFraction`) — irrelevant on G1/ZGC.

## 6. Revision checklist
- Why `-XX:MaxRAMPercentage` beats `-Xmx` in containers: ______
- Two flags to enable on every prod app: ______
- The "no heap dump" anti-pattern: ______
