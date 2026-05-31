# Fallacies of distributed computing

> Phase 3 · Tags: `distributed`

## 1. The concept
Eight assumptions distributed systems newcomers make — and which are all wrong:
1. The network is reliable.
2. Latency is zero.
3. Bandwidth is infinite.
4. The network is secure.
5. Topology doesn't change.
6. There is one administrator.
7. Transport cost is zero.
8. The network is homogeneous.

## 2. The rule / the why
Every distributed-systems bug traces back to violating one of these. They're not academic — they predict the bugs you'll write.

## 3. Java-specific behavior
- "Network is reliable" → set timeouts on **every** remote call (`HttpClient`, JDBC, Redis, gRPC). Defaults are often infinite.
- "Latency is zero" → never do a sync remote call inside a tight loop; batch.
- "Bandwidth is infinite" → don't return 50 MB JSON; paginate, stream, compress.
- "Network is secure" → mTLS between services; never trust the LAN.

## 4. System design angle
- Each fallacy maps to a defense: timeouts/retries, backpressure, compression/pagination, encryption, service discovery, multi-tenancy, cost monitoring, protocol negotiation.
- Chaos engineering deliberately violates these in prod to find which one bites you.

## 5. Common mistakes / traps
- Assuming a call to a "local" service in the same cluster is free.
- No timeout → one slow downstream hangs the entire app.
- Optimizing CPU when the bottleneck is N round trips.
- Treating retries as free — they amplify outages.

## 6. Revision checklist
- Name 4 fallacies: ______
- Pair each with a defense: ______
- Why timeouts are the #1 defense: ______
