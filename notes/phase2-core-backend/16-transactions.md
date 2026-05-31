# Transactions & isolation levels

> Phase 2 · Tags: `databases` `transactions`

## 1. The concept
**ACID**:
- **Atomicity**: all or nothing.
- **Consistency**: invariants hold before/after.
- **Isolation**: concurrent txns don't trip on each other.
- **Durability**: committed data survives crash.

**Isolation levels** (SQL standard, weakest → strongest):

| Level | Prevents |
|---|---|
| Read Uncommitted | (nothing useful — almost no real DB uses this) |
| Read Committed | Dirty reads |
| Repeatable Read | + Non-repeatable reads |
| Serializable | + Phantom reads (equivalent to serial execution) |

Anomalies:
- **Dirty read**: read uncommitted data.
- **Non-repeatable read**: same row, different value across two reads.
- **Phantom read**: same query, different rows appear.
- **Lost update**: two txns overwrite each other's changes.
- **Write skew**: each txn reads consistent state, both write, result violates invariant.

## 2. The rule / the why
Stronger isolation = fewer surprises but lower throughput (more locks/aborts). Pick the weakest level your invariants allow.

## 3. Java-specific behavior
- Spring: `@Transactional`. Default propagation is `REQUIRED`, default isolation is the DB default.
- Postgres default: Read Committed. MySQL InnoDB default: Repeatable Read.
- `@Transactional(isolation = Isolation.SERIALIZABLE, propagation = Propagation.REQUIRES_NEW)`.
- Rollback only on unchecked exceptions by default; use `rollbackFor = Exception.class` to include checked.
- Self-invocation (`this.foo()` calling another `@Transactional` method) bypasses the proxy → no new transaction.

## 4. System design angle
- Optimistic locking (`@Version`) for high-contention rows — fail and retry instead of holding a lock.
- Pessimistic (`SELECT ... FOR UPDATE`) for short critical sections.
- In distributed systems, ACID across services is hard → sagas (compensating actions) replace 2PC.

## 5. Common mistakes / traps
- Long transactions holding connections and locks → pool exhaustion + lock waits.
- `@Transactional` on a private method or self-invoked method → silently no transaction.
- Catching exceptions inside a transactional method → swallows rollback signal.
- Assuming Repeatable Read prevents lost updates — it doesn't (use `SELECT FOR UPDATE` or `@Version`).
- Running batch jobs at Serializable isolation → constant retries.

## 6. Revision checklist
- Define ACID's four letters: ______
- Two isolation anomalies and which level fixes each: ______
- Optimistic vs pessimistic locking: ______
- The Spring proxy self-invocation trap: ______
