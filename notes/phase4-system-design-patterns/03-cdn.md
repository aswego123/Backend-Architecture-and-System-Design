# CDNs & edge caching

> Phase 4 · Tags: `infra` `caching`

## 1. The concept
A **CDN** = a global network of caching servers (POPs — points of presence). Clients hit the nearest POP; cache hits return immediately, misses go to origin.

CDNs cache:
- Static assets (images, JS, CSS, video).
- Increasingly: API responses (with cache headers).
- Edge functions (Cloudflare Workers, Lambda@Edge) for personalization.

## 2. The rule / the why
Light = ~3ms per 1000km round trip. Without a CDN, a user in Tokyo waits 200+ ms for every asset from a US origin. CDN puts the asset 20ms away.

Also: absorbs traffic spikes, DDoS protection.

## 3. Java-specific behavior
Set cache headers your CDN respects:

```java
@GetMapping("/products/{id}")
public ResponseEntity<Product> get(@PathVariable Long id) {
    return ResponseEntity.ok()
        .cacheControl(CacheControl.maxAge(60, TimeUnit.SECONDS).cachePublic())
        .eTag(product.versionHash())
        .body(product);
}
```

- Use `ETag` + `If-None-Match` for revalidation (cheap 304s).
- `Vary` header for content negotiation (don't cache the wrong variant).

## 4. System design angle
- **Cache key**: URL + query + selected headers. Be careful with query strings (sort them) and cookies (strip).
- **Invalidation**: TTL is easy; explicit purge is slow (propagation). Versioned URLs (`/v123/main.css`) sidestep invalidation.
- **Origin shielding**: a regional cache absorbs misses so the origin sees only one request per miss.
- **Static + dynamic split**: cache aggressively for assets; bypass for personalized API responses.

## 5. Common mistakes / traps
- Cache-Control: no-cache on everything → CDN useless.
- Forgetting `Vary: Accept-Encoding` → serving brotli to clients expecting gzip.
- Cookies in URLs → every user is a cache miss.
- Long TTL with no invalidation strategy → stale content for hours.
- Caching personalized responses → user A sees user B's data.

## 6. Revision checklist
- Why a CDN reduces latency: ______
- One revalidation header pair: ______
- Versioned URL trick: ______
- The personalized-response cache trap: ______
