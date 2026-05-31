# Idempotency & delivery semantics

> Phase 3 · Tags: `distributed` `messaging`

## 1. The concept
- **At-most-once**: send and forget. May lose messages, never duplicates.
- **At-least-once**: retry until ack. May duplicate, never lose.
- **Exactly-once**: every message processed once. Requires cooperation between producer, broker, and consumer — usually achieved as "at-least-once + idempotent consumer".

**Idempotency**: doing the operation N times produces the same effect as doing it once.

## 2. The rule / the why
Networks lose acks, not just messages. If a consumer processes a message and then crashes before acking, the broker re-delivers → consumer must handle duplicates.

The pragmatic stance: design for at-least-once delivery + idempotent processing. "Exactly-once" guarantees are usually thin (depend on assumptions like "broker + sink in same transaction").

## 3. Java-specific behavior
- Kafka producer: `enable.idempotence=true` (default in modern clients) + `acks=all`.
- Kafka transactional API: read-process-write atomicity across topics (Kafka Streams uses this for "exactly-once").
- Consumer idempotency: deduplicate by message ID using a Redis set with TTL, or a DB unique constraint.
- HTTP: `Idempotency-Key` header (Stripe pattern) — server stores the key + response, returns cached response on retry.

## 4. System design angle
- Make every write API idempotent: client supplies a UUID, server upserts on it.
- Idempotent operations are safe to retry, which is the foundation of every retry policy.
- Some operations are naturally idempotent (`SET x = 5`); others aren't (`INCR counter`) — wrap them in an idempotency key.

## 5. Common mistakes / traps
- Believing the broker advertises "exactly-once" → assuming no dedup needed at consumer.
- Idempotency by "check then act" without a transaction → race condition.
- Idempotency keys with no expiry → unbounded growth.
- Forgetting that side effects (emails, payments) need their own idempotency keys downstream.

## 6. Revision checklist
- Three delivery semantics: ______
- Define idempotent operation: ______
- One way to deduplicate consumer-side: ______
- Why "exactly-once" is mostly marketing: ______
