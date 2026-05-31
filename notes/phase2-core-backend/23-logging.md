# Logging done right

> Phase 2 · Tags: `observability` `java`

## 1. The concept
Logs = ordered events emitted by your app. Useful logs are **structured** (JSON), **correlated** (trace/request IDs), and **actionable**.

Java logging stack: code uses **SLF4J** (facade); a backend implements it — **Logback** (Spring Boot default) or **Log4j2**.

Levels: TRACE < DEBUG < INFO < WARN < ERROR.

## 2. The rule / the why
- Plain text + grep doesn't scale.
- Structured JSON lets log aggregators (Loki, ELK, Datadog) parse fields without regex.
- Correlation IDs let you trace a single request across services.

## 3. Java-specific behavior
Always log via SLF4J:

```java
private static final Logger log = LoggerFactory.getLogger(OrderService.class);
log.info("order placed userId={} orderId={} amount={}", userId, orderId, amount);
```

- Use placeholders `{}` (not string concat) — args aren't formatted on disabled levels.
- MDC (Mapped Diagnostic Context): per-thread context attached to every log line. Used for `traceId`, `userId`, `tenantId`. Spring Sleuth / Micrometer Tracing auto-populates trace IDs.
- Use a JSON encoder in prod (`logstash-logback-encoder` or `log4j2.xml` JSONLayout).

## 4. System design angle
- Send logs to stdout/stderr in containers; let the platform ship them. Don't write to files inside the container.
- Sampling: at high volume, drop most INFO lines, keep all WARN/ERROR.
- Tie logs, metrics, and traces by the **same correlation ID** so you can pivot between them.

## 5. Common mistakes / traps
- `log.info("foo " + bar)` — concatenates even when INFO is off; use `{}`.
- Logging PII / secrets / full request bodies → privacy + compliance issues.
- Logging exceptions as a message instead of passing them as the last arg (`log.error("oops", ex)` keeps the stack).
- DEBUG enabled in prod for everything → flood + cost.
- Inconsistent field names across services (`user_id` vs `userId`) — define a logging schema.

## 6. Revision checklist
- Why use SLF4J as the facade: ______
- What MDC gives you: ______
- One thing never to log: ______
- The `{}` vs `+` performance reason: ______
