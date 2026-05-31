# Load balancers (L4 vs L7)

> Phase 4 · Tags: `infra` `traffic`

## 1. The concept
A **load balancer (LB)** sits in front of N backend servers and distributes incoming connections/requests.

- **L4 (transport)**: balances TCP connections. Sees IP + port, not URL or headers. Fast, simple. Examples: AWS NLB, IPVS, HAProxy in TCP mode.
- **L7 (application)**: speaks HTTP. Routes by path, header, host. Can terminate TLS, retry, modify headers. Examples: NGINX, Envoy, HAProxy HTTP mode, AWS ALB.

Algorithms: round-robin, least-connections, weighted, hash (consistent), random-2-choices.

## 2. The rule / the why
- LB enables horizontal scaling (add more servers) and survives single-server failures (health checks remove dead ones).
- L4 = throughput. L7 = smarts.

## 3. Java-specific behavior
- Spring Cloud LoadBalancer for client-side LB (in microservices). Replaces Ribbon.
- gRPC clients can do client-side LB with `pick_first` or `round_robin` policies.
- For HTTP/2: connection stickiness can defeat LBs — rotate connections periodically.

## 4. System design angle
- **Health checks**: active (LB pings backends) vs passive (LB observes failures). Both useful.
- **Session affinity ("sticky sessions")**: needed only when servers hold per-user state. Avoid by externalizing state to Redis.
- **DNS LB**: cheap, TTL-limited.
- **Anycast** + edge LBs route to the nearest POP.
- **Layered**: edge L7 → internal L4 → service.

## 5. Common mistakes / traps
- Sticky sessions for "performance" — couples a user to one server, kills failover.
- Health check that hits the DB → DB outage takes all backends out of rotation.
- L7 LB terminating TLS but forwarding plain HTTP inside an untrusted network.
- Long-lived H2 connections + LB → uneven load (LB picks once at connect time).

## 6. Revision checklist
- L4 vs L7 in one line: ______
- Two LB algorithms: ______
- One sticky-session pitfall: ______
- Why health checks must be cheap: ______
