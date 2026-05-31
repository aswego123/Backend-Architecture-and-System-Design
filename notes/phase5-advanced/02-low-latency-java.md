# Low-latency Java

> Phase 5 · Tags: `java` `performance` `low-latency`

## 1. The concept
Sub-millisecond / single-digit-millisecond systems (trading, real-time bidding, telecoms). Different rules than typical web services.

Techniques:
- **Lock-free / wait-free** data structures (LMAX Disruptor, JCTools queues).
- **Object pooling** / pre-allocation to avoid GC.
- **Off-heap memory** (`ByteBuffer.allocateDirect`, Chronicle Map) — no GC pressure.
- **Mechanical sympathy**: pin threads to cores, avoid false sharing, NUMA-aware allocation.
- **ZGC / Shenandoah** for sub-ms pause goals.

## 2. The rule / the why
At low latency, GC pauses, lock contention, and cache misses dominate. Standard "good enough" tools (HashMap, BlockingQueue, regular logging) become bottlenecks.

## 3. Java-specific behavior
- LMAX Disruptor: ring buffer + careful padding (avoid false sharing) → tens of millions of msgs/sec single-threaded.
- Chronicle Queue: persisted off-heap messaging, microsecond latency.
- Logging: async, structured, *don't* allocate in the hot path. Or use Aeron / binary protocols.
- Avoid `Optional`, streams, lambdas in the hottest loops (allocation pressure).

## 4. System design angle
- Co-locate with exchanges / data sources to cut network latency.
- Single-threaded "actor" per partition — eliminates locking.
- Trade throughput-of-many-cores for one-fast-core when latency rules.

## 5. Common mistakes / traps
- Allocating in the hot path → GC spikes.
- Logging string concatenation in hot paths.
- `synchronized` blocks where lock-free would do.
- Profiling on a noisy machine — pin CPUs, disable turbo boost.

## 6. Revision checklist
- Two low-latency techniques: ______
- Why off-heap memory helps: ______
- What "mechanical sympathy" means: ______
