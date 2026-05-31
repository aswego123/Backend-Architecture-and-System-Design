# Distributed tracing (OpenTelemetry)

> Phase 2 · Tags: `observability`

## 1. The concept
A **trace** = the full path of a request across services. A trace is a tree of **spans** (operations), each with start/end time, attributes, and a parent span.

**OpenTelemetry (OTel)**: the CNCF standard for traces, metrics, and logs. Vendor-neutral API + SDK + exporters.

Backends: Jaeger, Tempo, Honeycomb, Datadog, etc.

## 2. The rule / the why
In a microservice world, "why was that request slow?" is unanswerable from any single service's logs. Tracing stitches the picture together.

## 3. Java-specific behavior
- **Spring Boot 3 + Micrometer Tracing** is the modern integration (OTel under the hood).
- Add the OTel Java agent for **auto-instrumentation** — zero-code instrumentation of HTTP clients, JDBC, Kafka, etc.

```bash
java -javaagent:opentelemetry-javaagent.jar \
     -Dotel.service.name=order-service \
     -Dotel.exporter.otlp.endpoint=http://otel-collector:4317 \
     -jar app.jar
```

- W3C `traceparent` header propagates context across HTTP/gRPC calls.

## 4. System design angle
- Sampling: head-based (decide at trace start) vs tail-based (decide at trace end based on error/latency). Tail-based needs an OTel collector.
- Sample ~1–5% in steady state; bump to 100% during incidents.
- Pair traces with logs by including `trace_id` in MDC.
- Use traces to find unknown unknowns — slow downstream call you didn't realize existed.

## 5. Common mistakes / traps
- 100% sampling in prod → cost + perf hit.
- Trace context not propagating through async boundaries (executors, message queues) — wrap them.
- Adding huge attributes (request bodies) to spans → storage blow-up.
- Treating traces as logs — they're sampled and aggregated differently.
- Naming spans inconsistently (`HTTP GET` vs `GET /users/{id}`) — agree on conventions.

## 6. Revision checklist
- Trace vs span: ______
- What OpenTelemetry standardizes: ______
- Sampling strategies and when to use each: ______
- One way to lose trace context: ______
