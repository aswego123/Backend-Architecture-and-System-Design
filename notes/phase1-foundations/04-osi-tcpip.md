# OSI & TCP/IP layers

> Phase 1 · Tags: `networking`

## 1. The concept
- **OSI model** (7 layers, academic): Physical, Data Link, Network, Transport, Session, Presentation, Application.
- **TCP/IP model** (4 layers, practical): Link, Internet (IP), Transport (TCP/UDP), Application (HTTP, gRPC, DNS).
- Each layer adds a header and treats the layer above as opaque payload (encapsulation).

## 2. The rule / the why
- Separation of concerns: TCP doesn't care about Wi-Fi vs Ethernet; HTTP doesn't care about TCP retransmissions.
- Lets you swap one layer without rewriting the rest (IPv4 → IPv6, HTTP/1 → HTTP/2 → HTTP/3 over QUIC/UDP).

## 3. Java-specific behavior
- `java.net.Socket` / `ServerSocket` operate at the Transport layer (TCP).
- `DatagramSocket` for UDP.
- `URL` / `HttpClient` operate at the Application layer.
- `java.net.NetworkInterface` lets you query Link-layer info (MAC, MTU).

## 4. System design angle
- Load balancers operate at L4 (TCP, opaque payload, fast) or L7 (HTTP-aware, can route by path/header).
- A "VPN" usually means L3 tunneling; a "service mesh" intercepts L7.
- Latency budgets stack across layers; debugging means peeling them one at a time (Wireshark, `tcpdump`, `curl -v`).

## 5. Common mistakes / traps
- Confusing "TCP connection" with "HTTP request" — one TCP connection carries many requests (keep-alive, HTTP/2 multiplexing).
- Thinking ping/ICMP latency equals HTTP latency — it ignores TLS handshake and app processing.
- Forgetting MTU: large UDP packets get fragmented, often dropped silently.

## 6. Revision checklist
- Name the 4 TCP/IP layers and one protocol per layer: ______
- Why layering matters: ______
- L4 vs L7 load balancer in one line: ______
