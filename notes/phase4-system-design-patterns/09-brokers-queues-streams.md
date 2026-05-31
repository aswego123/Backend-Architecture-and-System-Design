# Brokers vs queues vs streams

> Phase 4 · Tags: `messaging`

## 1. The concept
Three overlapping concepts:

- **Message queue**: ordered, point-to-point. One consumer per message; broker deletes after ack. Examples: RabbitMQ (work queues), SQS, ActiveMQ.
- **Publish/subscribe**: one message → many subscribers. Each subscriber gets its own copy.
- **Event log / stream**: durable, replayable, append-only ordered log. Consumers track their position (offset). Multiple consumer groups can read independently. Example: Kafka, Pulsar, AWS Kinesis.

## 2. The rule / the why
- Queues decouple producer/consumer in time and capacity.
- Pub/sub decouples producer from N consumers.
- Streams add **replay** — late consumers can read history; new consumers can backfill.

Streams generalize queues: a single-consumer stream behaves like a queue.

## 3. Java-specific behavior
- Spring AMQP for RabbitMQ; Spring for Apache Kafka (`@KafkaListener`).
- JMS API as a portable abstraction (rarely used today; native clients preferred).
- Always handle poison messages: dead-letter queue (DLQ) for consumer failures.

## 4. System design angle
- **When to pick a queue**: short-lived tasks, work distribution among workers (e.g., image processing).
- **When to pick a stream**: event-sourced systems, multiple downstream consumers, need to replay.
- **Don't use a stream as a request/response channel** — latency and ergonomics are wrong; use RPC.
- **Outbox + stream** is the gold standard for reliable event publishing from a service.

## 5. Common mistakes / traps
- Using Kafka where a simple queue would do — operational overhead unjustified.
- Treating a queue as a database (long retention, querying by content) — wrong tool.
- No DLQ → one bad message blocks the consumer forever.
- No backpressure → producers overwhelm the broker (yes, brokers can fall over too).
- Ordering assumptions: most queues/streams give ordering per partition, not globally.

## 6. Revision checklist
- Queue vs pub/sub vs stream: ______
- Why streams enable replay: ______
- One thing not to do with Kafka: ______
- The DLQ purpose: ______
