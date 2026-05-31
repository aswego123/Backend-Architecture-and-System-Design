# TCP vs UDP

> Phase 1 · Tags: `networking` `transport`

## 1. The concept
- **TCP**: connection-oriented, reliable, ordered, byte-stream. 3-way handshake (SYN → SYN-ACK → ACK). Handles retransmissions, flow control (window), congestion control (slow start, AIMD).
- **UDP**: connectionless, unreliable, unordered, message (datagram) oriented. Tiny header, no handshake.

## 2. The rule / the why
- TCP exists because IP is best-effort: packets can be lost, duplicated, reordered. TCP gives you a clean stream on top of a messy pipe.
- UDP exists because TCP's guarantees cost latency. For real-time (voice, video, games) you'd rather drop a packet than wait 200 ms for retransmission.
- HTTP/3 runs on UDP (via QUIC) to escape TCP's head-of-line blocking.

## 3. Java-specific behavior
- `Socket` / `ServerSocket` = TCP. Stream-based (`InputStream`, `OutputStream`).
- `DatagramSocket` + `DatagramPacket` = UDP. Bounded per-packet.
- Tune with `setSoTimeout`, `setTcpNoDelay` (disable Nagle for low-latency small writes), `setKeepAlive`.

## 4. System design angle
- TCP: HTTP, gRPC (over HTTP/2), Postgres wire protocol, Kafka.
- UDP: DNS (single small query), DHCP, NTP, video conferencing, QUIC/HTTP/3, syslog.
- Inside a datacenter, TCP dominates because reliability matters more than the handshake overhead (which is sub-ms).

## 5. Common mistakes / traps
- "UDP is faster than TCP" — only on lossy networks or for tiny one-shot messages. On a clean LAN, TCP is fast.
- Forgetting TCP's slow start: short-lived connections never reach full throughput → use connection pooling.
- Sending UDP packets > MTU (~1500 bytes) → IP fragmentation → high loss.
- Assuming TCP guarantees delivery to the *application*: it only guarantees delivery to the kernel buffer. The app can still crash before reading.

## 6. Revision checklist
- One-line difference: ______
- Why HTTP/3 ditched TCP: ______
- One protocol per side: TCP ______, UDP ______
- The "TCP guarantees delivery" misconception: ______
