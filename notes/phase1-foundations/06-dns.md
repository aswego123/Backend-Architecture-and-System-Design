# DNS

> Phase 1 · Tags: `networking`

## 1. The concept
DNS translates names (`api.example.com`) into IPs (`93.184.216.34`). Hierarchical, distributed, cached at every level.

Resolution path: client → OS stub resolver → recursive resolver (ISP/8.8.8.8) → root → TLD (`.com`) → authoritative server → answer (with TTL).

Record types: `A` (IPv4), `AAAA` (IPv6), `CNAME` (alias), `MX` (mail), `TXT`, `NS`, `SRV`.

## 2. The rule / the why
- Humans need names; routers need IPs.
- Decentralization + caching make a global lookup take ~10 ms instead of hitting one server.
- TTL trades freshness for cache hit rate.

## 3. Java-specific behavior
- `InetAddress.getByName("host")` resolves via the OS.
- JVM caches positive lookups (controlled by `networkaddress.cache.ttl` in `java.security`). **Default is forever in older JVMs** — set it explicitly in long-running services or you'll keep talking to a dead IP after failover.
- HTTP clients (Apache, OkHttp, `HttpClient`) often have their own DNS caches.

## 4. System design angle
- DNS-based load balancing (return different IPs per query) is simple but TTL-limited.
- Service discovery in Kubernetes uses internal DNS (`service-name.namespace.svc.cluster.local`).
- DNS failover after a region outage is bottlenecked by TTL — keep production TTLs low (30–60s).
- DNS is a frequent outage cause (Facebook 2021, AWS Route53 incidents).

## 5. Common mistakes / traps
- Long TTLs blocking failover.
- JVM caching forever — set `networkaddress.cache.ttl=30`.
- Using `CNAME` at the apex of a domain (not allowed; use ALIAS/ANAME).
- Assuming DNS is "instant" — first lookup can take 50–200 ms cold.

## 6. Revision checklist
- The lookup chain in order: ______
- Why TTL matters for failover: ______
- The JVM DNS caching trap: ______
