# Collections framework internals

> Phase 1 · Tags: `java` `collections`

## 1. The concept
The contracts: `Collection` → `List`, `Set`, `Queue`; `Map` is separate. Each has multiple impls with different performance/ordering/thread-safety trade-offs.

## 2. The rule / the why
Pick the impl that matches your access pattern. The wrong choice (e.g., `LinkedList` for random access) silently kills performance.

## 3. Java-specific behavior

| Need | Use | Notes |
|---|---|---|
| Random access list | `ArrayList` | O(1) get; O(n) insert in middle |
| Frequent add/remove at ends, iterator-only | `ArrayDeque` | Use over `LinkedList` almost always |
| Insertion-ordered set | `LinkedHashSet` | |
| Sorted set | `TreeSet` | Red-black tree |
| Key-value, no order | `HashMap` | Treeifies bucket at 8 entries |
| Key-value, ordered | `TreeMap` / `LinkedHashMap` | |
| Thread-safe key-value | `ConcurrentHashMap` | Striped locking; iterator is weakly consistent |
| Thread-safe list | `CopyOnWriteArrayList` | Read-heavy; writes copy the array |
| Bounded blocking queue | `ArrayBlockingQueue` | For producer-consumer |
| Unbounded blocking queue | `LinkedBlockingQueue` | Watch out: unbounded = OOM risk |

Notes:
- `HashMap` is **not thread-safe**; Java 7 race could infinite-loop on resize.
- `Hashtable` and `Vector` are legacy synchronized — don't use.
- Immutable factories: `List.of`, `Set.of`, `Map.of` (since Java 9).

## 4. System design angle
- Caches start from `LinkedHashMap.removeEldestEntry()` for naive LRU; production uses Caffeine.
- `ConcurrentHashMap` is the workhorse of server-side state.
- `CopyOnWriteArrayList` shines for listener lists (rare writes, frequent iteration).

## 5. Common mistakes / traps
- Iterating + mutating a non-concurrent collection → `ConcurrentModificationException`.
- Using `LinkedList.get(i)` in a loop — O(n²) total.
- Storing mutable objects as `HashSet` elements / `HashMap` keys, then mutating them.
- `null` keys/values in `ConcurrentHashMap` are not allowed (unlike `HashMap`).
- Unbounded queues in executor pools → memory blow-up under load.

## 6. Revision checklist
- `ArrayList` vs `LinkedList` — which to default to: ______
- One thread-safe map and its trade-off: ______
- Why `CopyOnWriteArrayList` exists: ______
- The unbounded queue trap: ______
