# Spring MVC vs WebFlux

> Phase 2 · Tags: `java` `spring`

## 1. The concept
Two HTTP stacks in Spring:
- **MVC**: servlet-based, thread-per-request (blocking). Now pairs beautifully with **virtual threads**.
- **WebFlux**: reactive, non-blocking, runs on Netty (or Servlet 3.1+ async). Uses `Mono<T>` / `Flux<T>`.

## 2. The rule / the why
Reactive existed because thread-per-request didn't scale past ~10k concurrent connections on JVM. WebFlux trades simple code for high concurrency.

Virtual threads (Java 21+) largely close that gap — MVC + virtual threads handles the same load with normal blocking code. WebFlux still wins when you need backpressure or are wiring lots of reactive sources.

## 3. Java-specific behavior

| | MVC | WebFlux |
|---|---|---|
| Programming model | Imperative | Reactive (`Mono`/`Flux`) |
| Default server | Tomcat | Netty |
| DB drivers | JDBC (blocking) | R2DBC (reactive) |
| Threading | Thread-per-request (+virtual threads) | Event loop, few threads |
| Learning curve | Low | High |

Enable virtual threads in MVC (Spring Boot 3.2+):
```yaml
spring.threads.virtual.enabled: true
```

## 4. System design angle
- Streaming endpoints (SSE, long polling, chat) — WebFlux is natural.
- CRUD APIs over JDBC — MVC (especially + virtual threads).
- Reactive ecosystem (R2DBC, reactive Kafka, reactive Redis) is still smaller than blocking.

## 5. Common mistakes / traps
- Going WebFlux "for performance" then doing blocking JDBC inside — worst of both worlds.
- Mixing `Mono` and `CompletableFuture` without explicit bridges.
- Forgetting that `Mono`/`Flux` are lazy — nothing happens until something subscribes.
- Reactive stack traces are harder to read; use `Hooks.onOperatorDebug()` in dev only.

## 6. Revision checklist
- One-line difference: ______
- Why virtual threads weaken WebFlux's case: ______
- Two use cases where WebFlux still wins: ______
- The "blocking call in reactive pipeline" trap: ______
