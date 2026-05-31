# Java Memory Model (JMM) & happens-before

> Phase 1 · Tags: `java` `jvm` `concurrency`

## 1. The concept
The JMM defines **when a write by one thread becomes visible to another**, and what reorderings the compiler/CPU may perform.

Core idea: if action A *happens-before* action B, then A's effects are visible to B.

Happens-before edges are created by:
- Program order within one thread.
- `synchronized` (unlock → subsequent lock on the same monitor).
- `volatile` (write → subsequent read of the same variable).
- `Thread.start()` and `Thread.join()`.
- `final` field freeze at end of constructor (publication guarantee).
- `java.util.concurrent` primitives (`Lock`, `Atomic*`, `Semaphore`, etc.).

## 2. The rule / the why
Without happens-before, the JIT and CPU are free to reorder/cache. A thread can read a stale value forever. Race-free code requires either immutability or an explicit happens-before edge between the writer and reader.

## 3. Java-specific behavior
- `volatile`: guarantees visibility and prevents reordering across the volatile access. **Not atomic for compound ops** (`v++` is still racy).
- `final` fields: safely publishable if the reference doesn't escape the constructor.
- `synchronized` and `j.u.c.locks` provide both mutual exclusion *and* the memory barrier.

```java
class Flag {
    private volatile boolean ready = false;  // visibility guaranteed
    private int data;
    void producer() { data = 42; ready = true; }      // write data happens-before write ready
    void consumer() { if (ready) System.out.println(data); }  // sees 42
}
```

## 4. System design angle
- Every lock-free algorithm (queues, ring buffers, LMAX Disruptor) rests on JMM semantics.
- "Double-checked locking" only works since Java 5 because of clarified JMM rules (`volatile` field).
- Reactive frameworks rely on happens-before guarantees of their schedulers.

## 5. Common mistakes / traps
- Using a non-volatile boolean to stop a thread → loop never sees the change.
- `v++` on a `volatile` int → still a race; use `AtomicInteger`.
- Publishing a partially-constructed object via a non-final field (`this` escape).
- Assuming `synchronized` is "slow" — modern JVMs use biased/thin locks; usually it's fine.

## 6. Revision checklist
- Define happens-before: ______
- Two ways to create a happens-before edge: ______
- Why `volatile v++` is still wrong: ______
- The `final` field publication guarantee: ______
