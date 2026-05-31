# Core data structures (backend subset)

> Phase 1 · Tags: `dsa`

## 1. The concept
Quick reference of the structures that show up across backend systems:

| Structure | Use | Key property |
|---|---|---|
| Hash map | O(1) avg lookup by key | Needs good hash; rehash on grow |
| Balanced BST (Red-Black, AVL) | Ordered set with O(log n) ops | In-order traversal |
| Heap (binary) | Priority queue, top-K | O(log n) push/pop |
| Trie | Prefix lookup (autocomplete, routing) | Per-character branching |
| Bloom filter | Probabilistic set membership | False positives, no false negatives |
| Skip list | Ordered set, simpler than balanced BST | Used by Redis sorted sets |

## 2. The rule / the why
- Pick the structure that matches the **access pattern**, not "the smartest" one.
- Probabilistic structures trade accuracy for huge memory savings — crucial at scale.

## 3. Java-specific behavior
- `HashMap`: array of buckets + linked list, converts to red-black tree at bucket size 8 (since Java 8).
- `TreeMap`: red-black tree, ordered.
- `PriorityQueue`: binary heap, **not thread-safe**, **not sorted on iteration**.
- `LinkedHashMap`: hash map + insertion-order linked list (great LRU base via `removeEldestEntry`).
- No built-in trie or bloom filter — use Guava (`BloomFilter`) or Apache Commons.

## 4. System design angle
- Bloom filters in front of disk reads (Cassandra, RocksDB) skip 99% of misses.
- Tries power URL routing (Spring `PathPatternParser`), DNS, autocomplete.
- Heaps power priority queues in schedulers (e.g., Quartz, Kubernetes scheduler hooks).
- Skip lists power Redis `ZSET` and LevelDB memtables.

## 5. Common mistakes / traps
- Using `HashMap` from multiple threads → infinite loop in Java 7, data corruption in Java 8. Use `ConcurrentHashMap`.
- Mutating an object after using it as a `HashMap` key → can't find it again.
- Bloom filter sized too small → false positive rate explodes.
- `PriorityQueue.iterator()` is not in priority order — drain it instead.

## 6. Revision checklist
- One use case per structure: ______
- Why `HashMap` is not thread-safe: ______
- When to reach for a bloom filter: ______
