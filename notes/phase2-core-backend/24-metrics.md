# Metrics (Micrometer + Prometheus)

> Phase 2 · Tags: `observability` `java`

## 1. The concept
**Metrics** = numeric measurements over time. Three core types:
- **Counter**: monotonically increases (requests served, errors).
- **Gauge**: instantaneous value (queue size, memory usage).
- **Histogram / Timer**: distribution of values (latency).

**Micrometer**: Java's vendor-neutral metrics facade ("SLF4J for metrics"). Backends: Prometheus, Datadog, CloudWatch, etc.

**Prometheus**: pull-based metrics DB. Scrapes `/actuator/prometheus`. PromQL for queries. Pairs with **Grafana** for dashboards.

## 2. The rule / the why
You can't improve what you don't measure. Metrics are **cheap** (numeric) compared to logs and traces — sample everything.

The **four golden signals** (Google SRE): Latency, Traffic, Errors, Saturation. If you have those per service, you can spot any major incident.

## 3. Java-specific behavior
Spring Boot + Actuator auto-instruments HTTP server, HikariCP pool, JVM (GC, heap, threads), Tomcat/Jetty.

```java
@Autowired MeterRegistry registry;

Counter signups = registry.counter("user.signups", "source", "web");
signups.increment();

Timer.Sample sample = Timer.start(registry);
doWork();
sample.stop(registry.timer("work.duration"));
```

```yaml
management.endpoints.web.exposure.include: health,info,prometheus,metrics
management.metrics.distribution.percentiles.http.server.requests: 0.5,0.95,0.99
```

## 4. System design angle
- Histograms vs summaries: histograms aggregate across instances; summaries don't. Almost always pick histograms.
- High-cardinality tags (e.g., `userId`) explode storage costs. Keep tag values bounded.
- Per-service dashboard template: golden signals + GC + DB pool + business KPIs.
- Alert on SLO burn rate, not on raw thresholds.

## 5. Common mistakes / traps
- Tagging with user IDs / request IDs → cardinality explosion.
- Averaging latency — useless. Use p50/p95/p99.
- Custom metric names that collide with auto-instrumented ones.
- Exposing `/actuator/prometheus` publicly without auth.
- Recording only success counts → can't compute error rate.

## 6. Revision checklist
- Counter vs gauge vs histogram: ______
- The four golden signals: ______
- Why averages lie: ______
- One cardinality trap: ______
