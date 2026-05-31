# Phase 4 — System Design Patterns

Goal: recognize and apply the recurring building blocks of large systems.

## Index

### 4.1 Traffic & topology
- [Load balancers (L4 vs L7)](01-load-balancers.md)
- [Reverse proxies & API gateways](02-reverse-proxies-gateways.md)
- [CDNs & edge caching](03-cdn.md)
- [Rate limiting algorithms](04-rate-limiting.md)

### 4.2 Data at scale
- [OLTP vs OLAP; row vs column stores](05-oltp-vs-olap.md)
- [NoSQL families](06-nosql-families.md)
- [Search engines (Elasticsearch)](07-search-engines.md)
- [Time-series & analytics stores](08-timeseries.md)

### 4.3 Messaging & streaming
- [Brokers vs queues vs streams](09-brokers-queues-streams.md)
- [Kafka deep dive](10-kafka.md)
- [Outbox pattern & CDC](11-outbox-cdc.md)
- [Event sourcing & CQRS](12-event-sourcing-cqrs.md)

### 4.4 Service architecture
- [Monolith vs microservices](13-monolith-vs-microservices.md)
- [Service discovery & config](14-service-discovery.md)
- [Circuit breakers, bulkheads, retries (Resilience4j)](15-resilience-patterns.md)
- [Sidecars & service mesh](16-service-mesh.md)
- [Multi-tenancy](17-multi-tenancy.md)

### 4.5 Security
- [AuthN, AuthZ, OAuth2 flows](18-authn-authz.md)
- [Secrets management](19-secrets.md)
- [OWASP Top 10 for APIs](20-owasp-api.md)
- [Encryption: at rest, in transit](21-encryption.md)

### 4.6 Reliability engineering
- [SLI/SLO/SLA & error budgets](22-slo.md)
- [Deploy strategies & feature flags](23-deploy-strategies.md)
- [Chaos engineering](24-chaos.md)
- [Disaster recovery (RPO/RTO)](25-dr.md)
