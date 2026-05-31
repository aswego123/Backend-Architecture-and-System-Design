# gRPC & Protobuf

> Phase 2 · Tags: `api` `grpc`

## 1. The concept
**Protocol Buffers (Protobuf)**: schema language + compact binary serialization. You write `.proto` files; the compiler generates type-safe code in many languages.

**gRPC**: RPC framework using Protobuf over HTTP/2. Four call types:
- Unary (request → response).
- Server streaming (request → stream of responses).
- Client streaming (stream → response).
- Bidirectional streaming.

## 2. The rule / the why
- Smaller, faster than JSON over HTTP/1 (binary, header compression, multiplexed).
- Strict typed schema → no "what does this field mean?".
- Streaming is first-class.
- Drawback: not browser-friendly without gRPC-Web; harder to debug than JSON.

## 3. Java-specific behavior
- `protoc` + `protoc-gen-grpc-java` generate stubs.
- Server: extend the generated `*ImplBase` class, register with `ServerBuilder`.
- Client: `ManagedChannelBuilder.forAddress(...).build()` + generated stub.
- Spring integration via `grpc-spring-boot-starter`.

```proto
service UserService {
  rpc GetUser(GetUserRequest) returns (User);
  rpc StreamEvents(EventFilter) returns (stream Event);
}
```

## 4. System design angle
- Service-to-service comms inside a cluster: gRPC dominates (typed, fast).
- External/public APIs: usually REST/JSON for tooling and browser reach.
- gRPC over a service mesh gives mTLS, retries, and load balancing automatically.
- Schema evolution rules: never reuse field numbers; add fields with new numbers; remove only by deprecation.

## 5. Common mistakes / traps
- Reusing or renumbering proto fields → silent data corruption.
- No deadlines on calls → cascading hangs (always set per-call timeout).
- Long-lived gRPC channels hitting a single backend due to HTTP/2 connection stickiness — use load-balancing policies.
- Returning huge streams without flow control / backpressure.
- Treating `optional` and "default value" the same in proto3 — different (use `optional` keyword if you need presence).

## 6. Revision checklist
- gRPC vs REST in one trade-off: ______
- The 4 streaming modes: ______
- Schema evolution rule: ______
- The "no deadline" trap: ______
