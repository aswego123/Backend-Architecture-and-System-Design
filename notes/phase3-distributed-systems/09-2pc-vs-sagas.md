# 2PC vs Sagas

> Phase 3 · Tags: `distributed` `transactions`

## 1. The concept
Two ways to coordinate work spanning multiple services / data stores.

**2PC (Two-Phase Commit)**:
1. **Prepare**: coordinator asks all participants "can you commit?"; each writes to disk and replies yes/no.
2. **Commit/Abort**: if all yes, coordinator says commit; else abort.

**Saga**: replace one big transaction with a sequence of local transactions, each with a **compensating** action.
- Choreography: services emit events, each reacts.
- Orchestration: a central saga executor drives the steps.

## 2. The rule / the why
- 2PC gives ACID across systems — but the coordinator is a single point of failure, and participants can block forever waiting for it.
- Sagas accept that some intermediate states are visible; compensating actions restore consistency on failure. Scales much better.

In microservices, sagas are the default; 2PC is rare (XA transactions are slow and fragile).

## 3. Java-specific behavior
- JTA / XA: 2PC across JMS + JDBC inside a single process.
- Spring `@Transactional` doesn't do distributed transactions by default.
- Saga frameworks: Axon, Eventuate, Camunda. Or hand-roll with an outbox + message broker.

## 4. System design angle
- **Outbox pattern**: write your local DB change and an "event to publish" in the *same* local transaction. A separate process reads the outbox and publishes → reliable event publishing without 2PC.
- Compensating actions are **not undos** — they're new operations ("refund payment" not "delete charge").
- Some steps can't be compensated (sending an email) → design the saga so non-reversible steps come last.

## 5. Common mistakes / traps
- Reaching for 2PC because "transactions are familiar" — pays in latency and availability.
- Saga without persistent state → on crash, no idea what to compensate.
- Forgetting idempotency in compensating actions → double refunds.
- Choreographed sagas with too many services → no one understands the flow; switch to orchestration.

## 6. Revision checklist
- Why 2PC is avoided in microservices: ______
- Saga's core idea: ______
- The outbox pattern in one line: ______
- One step that can't be compensated: ______
