# Learning Map — Backend Engineering (Java) & System Design

> A learning-first roadmap. Each topic later gets its own note under `notes/` following the [topic template](./TOPIC_TEMPLATE.md):
> **1) Concept · 2) Rule/Why · 3) Java-specific · 4) System design angle · 5) Traps · 6) Revision checklist**

Legend: **[E]** Essential · **[O]** Optional (revisit after the phase) · **[J]** Java-specific · **[SD]** System design–specific

---

## Phase 1 — Foundations (the bedrock)

Goal: build the mental models everything else rests on. Skip these and every later topic feels like memorization.

### 1.1 Computer & OS fundamentals
- [E] Processes vs threads vs coroutines
- [E] CPU scheduling, context switches, cost of a syscall
- [E] Memory model: stack vs heap, virtual memory, page cache
- [E] Blocking vs non-blocking I/O, sync vs async, epoll/kqueue (conceptually)
- [O] File systems, inodes, fsync, write durability

### 1.2 Networking
- [E] OSI/TCP-IP layers (just enough to reason about latency)
- [E] TCP vs UDP, 3-way handshake, congestion control intuition
- [E] DNS resolution path
- [E] HTTP/1.1 vs HTTP/2 vs HTTP/3 (head-of-line blocking, multiplexing)
- [E] TLS handshake, certificates, mTLS
- [O] WebSockets, SSE, long polling

### 1.3 Data structures & algorithms (backend-relevant subset)
- [E] Hash maps, balanced trees, heaps, tries, bloom filters
- [E] LSM trees and B+ trees (you'll meet them in every DB)
- [E] Consistent hashing
- [O] Skip lists, HyperLogLog, Count-Min Sketch

### 1.4 Java language & JVM core **[J]**
- [E] JMM (Java Memory Model): happens-before, volatile, final field semantics
- [E] Garbage collection: generational hypothesis, G1 vs ZGC vs Shenandoah
- [E] Class loading, bytecode basics, JIT (C1/C2)
- [E] Exceptions: checked vs unchecked, try-with-resources
- [E] Collections framework internals (HashMap, ConcurrentHashMap, ArrayList, LinkedList trade-offs)
- [E] Generics, type erasure, variance (`? extends`, `? super`)
- [O] `Unsafe`, `VarHandle`, Project Panama/Loom overview

---

## Phase 2 — Core Backend (Java)

Goal: build, test, and run production-quality services.

### 2.1 Concurrency in Java **[J]**
- [E] `Thread`, `Runnable`, `Callable`, `Future`
- [E] `ExecutorService`, thread pools, sizing rules
- [E] `synchronized`, `ReentrantLock`, `ReadWriteLock`, `StampedLock`
- [E] `java.util.concurrent` atomics, `ConcurrentHashMap`, `CopyOnWriteArrayList`
- [E] `CompletableFuture` and async composition
- [E] Virtual threads (Project Loom) and structured concurrency
- [O] Reactive streams (`Flow`, Reactor, RxJava)

### 2.2 Build, packaging, runtime **[J]**
- [E] Maven and Gradle (lifecycle, dependency resolution, BOMs)
- [E] JAR vs fat JAR vs JLink/JPackage images
- [E] JVM flags you actually need (`-Xmx`, `-XX:+UseG1GC`, `-XX:MaxRAMPercentage`)
- [O] GraalVM native image trade-offs

### 2.3 Spring ecosystem **[J]**
- [E] Spring Core: IoC container, bean lifecycle, scopes, `@Configuration`
- [E] Spring Boot auto-configuration and starters
- [E] Spring MVC vs WebFlux (when to pick which)
- [E] Spring Data JPA: repositories, transactions, `EntityManager`, N+1 trap
- [E] Spring Security: filters chain, authentication vs authorization, JWT/OAuth2
- [E] Bean validation (`jakarta.validation`)
- [O] Spring Cloud (Config, Gateway, OpenFeign)

### 2.4 APIs
- [E] REST: resource modeling, status codes, idempotency, pagination, versioning
- [E] OpenAPI/Swagger contract-first design
- [E] gRPC and Protobuf (schema evolution, streaming modes)
- [E] GraphQL basics (when it helps, when it hurts)
- [O] JSON:API, HATEOAS

### 2.5 Relational databases
- [E] SQL beyond basics: joins, window functions, CTEs, `EXPLAIN ANALYZE`
- [E] Indexing: B+ tree, covering index, composite index ordering
- [E] Transactions and isolation levels (RC, RR, Serializable) and the anomalies each prevents
- [E] MVCC (PostgreSQL focus)
- [E] Connection pooling (HikariCP) and pool sizing **[J]**
- [O] Stored procedures, materialized views, partitioning

### 2.6 Caching
- [E] Cache patterns: cache-aside, read-through, write-through, write-behind
- [E] TTL, eviction policies (LRU, LFU, ARC), stampede protection
- [E] Redis data structures and when to use each
- [O] Caffeine (in-process cache) **[J]**

### 2.7 Testing
- [E] JUnit 5, AssertJ, Mockito **[J]**
- [E] Testcontainers for real DBs/queues in tests **[J]**
- [E] Contract testing (Pact)
- [O] Mutation testing (PIT), property-based testing (jqwik)

### 2.8 Observability
- [E] Structured logging (SLF4J + Logback/Log4j2) **[J]**
- [E] Metrics (Micrometer + Prometheus) **[J]**
- [E] Distributed tracing (OpenTelemetry, W3C trace context)
- [E] The four golden signals (latency, traffic, errors, saturation)

### 2.9 DevOps essentials
- [E] Docker images, multi-stage builds, distroless
- [E] CI/CD basics (GitHub Actions or similar)
- [E] One cloud at intro level (AWS or GCP): compute, object storage, managed DB, IAM
- [O] Kubernetes (Deployment, Service, ConfigMap, HPA)

---

## Phase 3 — Distributed Systems (the hard truths)

Goal: reason about failure, time, and consistency across machines.

- [E] [SD] Fallacies of distributed computing
- [E] [SD] CAP and PACELC theorems (what they actually say vs the myths)
- [E] [SD] Consistency models: linearizable, sequential, causal, eventual, read-your-writes
- [E] [SD] Time and order: physical clocks, logical clocks, vector clocks, hybrid logical clocks
- [E] [SD] Replication: leader-follower, multi-leader, leaderless (Dynamo-style)
- [E] [SD] Quorums (R + W > N), read repair, anti-entropy
- [E] [SD] Partitioning/sharding: range, hash, consistent hashing, hot keys
- [E] [SD] Consensus: Paxos (intuition), Raft (deeply), leader election
- [E] [SD] Two-phase commit and why it's avoided; sagas as the alternative
- [E] [SD] Idempotency, exactly-once vs at-least-once vs at-most-once delivery
- [E] [SD] Failure detection, timeouts, retries with backoff + jitter
- [E] [SD] Backpressure and flow control
- [O] [SD] CRDTs, gossip protocols, Byzantine fault tolerance

---

## Phase 4 — System Design Patterns (building blocks at scale)

Goal: recognize the recurring patterns behind every large system.

### 4.1 Traffic & topology
- [E] Load balancers: L4 vs L7, algorithms, health checks, sticky sessions
- [E] Reverse proxies (NGINX, Envoy), API gateways
- [E] CDNs, edge caching, cache invalidation strategies
- [E] Rate limiting (token bucket, leaky bucket, sliding window)

### 4.2 Data at scale
- [E] OLTP vs OLAP; row stores vs column stores
- [E] NoSQL families: KV, document, wide-column, graph — when each wins
- [E] Search engines (Elasticsearch/OpenSearch): inverted index, relevance basics
- [E] Time-series databases (intuition, write patterns)
- [E] Data lakes, warehouses, lakehouses (conceptual)

### 4.3 Messaging & streaming
- [E] Brokers vs queues vs streams
- [E] Kafka deeply: partitions, offsets, consumer groups, retention, exactly-once semantics
- [E] Outbox pattern, change data capture (Debezium)
- [E] Event-driven architecture, event sourcing, CQRS (and their costs)
- [O] RabbitMQ, NATS, Pulsar comparisons

### 4.4 Service architecture
- [E] Monolith vs modular monolith vs microservices (decision criteria, not dogma)
- [E] Service discovery, configuration management
- [E] Circuit breakers, bulkheads, timeouts, retries (Resilience4j) **[J]**
- [E] Sidecars and service mesh (Istio/Linkerd, conceptual)
- [E] Multi-tenancy patterns

### 4.5 Security
- [E] AuthN vs AuthZ; OAuth2 flows, OIDC, JWT pitfalls
- [E] Secrets management (Vault, cloud KMS)
- [E] OWASP Top 10 for APIs
- [E] Encryption in transit and at rest, key rotation
- [O] Zero-trust architecture

### 4.6 Reliability engineering
- [E] SLI/SLO/SLA, error budgets
- [E] Blue/green, canary, rolling deploys, feature flags
- [E] Chaos engineering basics
- [E] Disaster recovery: RPO, RTO, backup strategies

---

## Phase 5 — Advanced / Specialization

Goal: depth in the areas your career or interest points to.

- [O] JVM performance tuning: GC tuning, JFR, async-profiler, escape analysis **[J]**
- [O] Low-latency Java: lock-free data structures, off-heap memory, Disruptor **[J]**
- [O] Project Loom deep dive and structured concurrency patterns **[J]**
- [O] Database internals: write-ahead logs, MVCC garbage collection, query planners
- [O] Designing storage engines (LSM vs B-tree trade-offs in depth)
- [O] Stream processing engines (Flink, Kafka Streams) windowing & state
- [O] Geo-distributed systems, multi-region active-active
- [O] Real-time systems: WebRTC, video pipelines
- [O] ML serving infrastructure (feature stores, model gateways)
- [O] Cost-aware architecture (FinOps thinking)

---

## Practice track (run in parallel from Phase 2 onward)

Build these progressively — each one forces you into the next layer of concepts:

1. URL shortener (hashing, DB design, caching)
2. Rate limiter as a library + as a service (algorithms, Redis, distributed counters)
3. Pastebin / file uploader (object storage, signed URLs)
4. Chat service (WebSockets, fan-out, presence)
5. News feed (push vs pull, ranking, caching)
6. Distributed job scheduler (leader election, idempotency, retries)
7. Mini Kafka or mini Raft implementation **[J]**

For each project, write a one-page design doc *before* coding: requirements, capacity estimates, API, data model, failure modes.

---

## How to use this map

- Pick **one** topic at a time. Open a note under `notes/<phase>/<topic>.md` using the template.
- Don't read passively — write the "Rule/Why" section in your own words before checking a source.
- Revisit the "Revision checklist" of older topics weekly. Spaced repetition beats re-reading.
- When a topic feels abstract, build the smallest possible thing that exercises it.
