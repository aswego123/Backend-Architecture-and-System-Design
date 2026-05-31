# Real-time systems (WebRTC, video pipelines)

> Phase 5 · Tags: `realtime` `media`

## 1. The concept
Real-time = sub-second, often sub-100ms, end-to-end. Use cases: voice/video calls, live streaming, multiplayer games, collaborative editing.

Key tech:
- **WebSockets**: bidirectional TCP-based stream, browser-to-server.
- **SSE (Server-Sent Events)**: server → client one-way over HTTP.
- **WebRTC**: peer-to-peer audio/video/data over UDP, with NAT traversal (STUN/TURN/ICE).
- **SFU / MCU**: media servers that fan out video streams in multi-party calls.
- **Low-latency streaming**: HLS-LL, WebRTC, RTMP.

## 2. The rule / the why
TCP guarantees order/reliability at the cost of latency (head-of-line blocking). For voice/video, losing a packet is fine; waiting 200ms for retransmit is not — hence WebRTC over UDP.

## 3. Java-specific behavior
- WebSocket support in Spring (`@MessageMapping`, STOMP).
- Pion/Janus/mediasoup for media servers (usually not Java; Java apps talk to them via APIs).
- Netty for custom protocols and high-concurrency sockets.

## 4. System design angle
- Connection capacity is the bottleneck — design for 10k–100k concurrent WS per server (event loops, Netty, virtual threads).
- Presence systems (who's online) need pub/sub + per-user fan-out — Redis or a dedicated service.
- Edge points-of-presence reduce RTT to global users.

## 5. Common mistakes / traps
- WebSockets with thread-per-connection → can't scale past ~10k.
- Trying to do video in your app code — use SFUs.
- Forgetting reconnect/resume logic for mobile clients on flaky networks.

## 6. Revision checklist
- WebSocket vs SSE vs WebRTC: ______
- Why WebRTC uses UDP: ______
- What an SFU does: ______
