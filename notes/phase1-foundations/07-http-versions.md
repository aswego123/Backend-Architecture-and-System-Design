# HTTP/1.1, HTTP/2, HTTP/3

> Phase 1 · Tags: `networking` `http`

## 1. The concept
- **HTTP/1.1**: text-based, one request per connection at a time (or pipelined, rarely works). Keep-alive reuses the TCP connection.
- **HTTP/2**: binary framing, multiplexed streams over one TCP connection, header compression (HPACK), server push (deprecated in practice).
- **HTTP/3**: HTTP semantics over **QUIC** (UDP-based). Independent streams, no TCP head-of-line blocking, 0-RTT resumption.

## 2. The rule / the why
- **H1 problem**: one request blocks the connection → browsers open 6 connections per origin. Wasteful.
- **H2 problem**: multiplexing solves app-layer HOL, but TCP still has packet-level HOL — one lost packet stalls all streams.
- **H3 fix**: QUIC streams are independent at transport level; one loss only stalls one stream.

## 3. Java-specific behavior
- `java.net.http.HttpClient` (since Java 11) supports H1 and H2.
- H3 support is incoming via incubator/third-party (Netty, Jetty have it; JDK `HttpClient` not yet GA as of writing).
- Spring MVC works over any; WebFlux + Reactor Netty supports H2 natively.

```java
var client = HttpClient.newBuilder().version(HttpClient.Version.HTTP_2).build();
```

## 4. System design angle
- gRPC mandates HTTP/2 (needs streaming + multiplexing).
- HTTP/2 is the default for service-to-service inside meshes.
- CDNs and edge networks ship H3 to mobile clients on flaky networks for the latency win.

## 5. Common mistakes / traps
- Believing H2 fixes all latency — TCP HOL still bites on lossy links.
- Misusing server push — almost always worse than `<link rel=preload>`.
- Long-lived H2 connections + load balancer → uneven load (LB picks server at connect time). Use periodic connection rotation.
- Forgetting QUIC needs UDP open through firewalls.

## 6. Revision checklist
- One-line difference H1 → H2 → H3: ______
- What "HOL blocking" means at each layer: ______
- Why gRPC requires H2: ______
- The load-balancing pitfall with long-lived H2 connections: ______
