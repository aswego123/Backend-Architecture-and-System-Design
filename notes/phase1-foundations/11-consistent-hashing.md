# Consistent hashing

> Phase 1 · Tags: `dsa` `distributed`

## 1. The concept
Map both **keys** and **servers** onto a ring (hash output space, e.g., 0..2³²-1). A key is owned by the next server clockwise.

Adding/removing one server moves only `~K/N` keys instead of nearly all keys (which naive `hash(key) % N` would do).

**Virtual nodes**: each physical server occupies many points on the ring → even load distribution.

## 2. The rule / the why
Naive sharding by modulo `% N` breaks the entire mapping when `N` changes. Consistent hashing localizes the disruption — critical for caches and partitioned stores where remapping = mass cache miss or expensive data movement.

## 3. Java-specific behavior
- No standard JDK implementation; use Guava's `Hashing.consistentHash(long, int)` for simple cases (jump consistent hash).
- For ring-based with vnodes: roll your own with a `TreeMap<Long, Node>` and `ceilingEntry(hash)`.

```java
TreeMap<Long, Node> ring = new TreeMap<>();
for (Node n : nodes)
    for (int i = 0; i < 100; i++)
        ring.put(hash(n.id() + ":" + i), n);
Node owner = ring.ceilingEntry(hash(key)) != null
    ? ring.ceilingEntry(hash(key)).getValue()
    : ring.firstEntry().getValue();
```

## 4. System design angle
- Used by: Memcached clients, Cassandra (token ring), DynamoDB, Akka cluster sharding, many CDN edge mappings.
- Load balancers can use it for **session affinity** without state.
- Jump hash (Google) is a faster, allocation-free variant when you don't need add/remove of arbitrary nodes.

## 5. Common mistakes / traps
- Too few vnodes → uneven load (hot spots).
- Using a weak hash (e.g., `String.hashCode()`) → clustering. Use MurmurHash3 or xxHash.
- Forgetting that adding a node still moves data — plan for the brief warm-up period.
- Treating consistent hashing as a load balancer for general HTTP traffic — it's for *key-affinity* problems.

## 6. Revision checklist
- Why `% N` is bad: ______
- What vnodes solve: ______
- One real system that uses it: ______
- A good vs bad hash function choice: ______
