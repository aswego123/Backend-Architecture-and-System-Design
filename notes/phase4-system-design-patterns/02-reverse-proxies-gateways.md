# Reverse proxies & API gateways

> Phase 4 · Tags: `infra` `api`

## 1. The concept
- **Reverse proxy** (NGINX, Envoy): sits between clients and backends. Does TLS termination, compression, static caching, request routing.
- **API gateway** (Kong, Apigee, AWS API Gateway, Spring Cloud Gateway): reverse proxy + API-specific features — auth, rate limiting, request transformation, schema validation, analytics, per-API quotas.

## 2. The rule / the why
- Centralize cross-cutting concerns once instead of in every service.
- Provide a single, stable entry point clients can rely on while internals change.

## 3. Java-specific behavior
- Spring Cloud Gateway (reactive, on Netty) — define routes + filters in YAML or code.
- Use for: JWT validation, rate limiting (Redis-backed), request/response logging, fan-out aggregation (BFF).

```yaml
spring.cloud.gateway.routes:
  - id: users
    uri: lb://user-service
    predicates: [Path=/users/**]
    filters:
      - StripPrefix=0
      - name: RequestRateLimiter
        args: { redis-rate-limiter.replenishRate: 10, redis-rate-limiter.burstCapacity: 20 }
```

## 4. System design angle
- Gateway is itself a tier — scale it horizontally, replicate config (often via control plane like Istio/Envoy xDS).
- Don't put business logic in the gateway. Auth, routing, rate limit: yes. Order calculation: no.
- "API gateway per BFF" pattern: one tailored gateway per client type (web, mobile, partner).

## 5. Common mistakes / traps
- Gateway as the single point of failure → must be HA.
- Routing rules sprawling into hundreds of lines no one understands.
- Doing protocol translation (REST → SOAP → gRPC) in the gateway — hides complexity.
- Re-implementing auth in each downstream service "just in case".

## 6. Revision checklist
- Reverse proxy vs API gateway: ______
- Three things a gateway does well: ______
- One thing it shouldn't do: ______
