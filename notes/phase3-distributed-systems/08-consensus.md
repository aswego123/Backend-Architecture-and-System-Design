# Consensus: Paxos & Raft

> Phase 3 · Tags: `distributed` `consensus`

## 1. The concept
**Consensus** = a group of nodes agreeing on a value (or sequence of values) despite failures. Used for: leader election, replicated state machines, distributed locks, configuration stores.

- **Paxos**: the original, mathematically elegant, notoriously hard to implement.
- **Raft**: designed to be understandable. Same guarantees as Paxos. Used by etcd, Consul, CockroachDB, TiKV, MongoDB (similar protocol).

Raft basics:
1. **Leader election**: nodes vote for a leader by term number.
2. **Log replication**: leader appends entries to its log, replicates to followers, commits when a majority acks.
3. **Safety**: only a node with an up-to-date log can become leader.

## 2. The rule / the why
You need agreement to do *anything* coordinated across nodes safely: who's the leader, which write committed, what the config is.

Without consensus → split-brain, lost writes, double-acks.

## 3. Java-specific behavior
- Java consensus libs: Apache Ratis (Raft), Atomix, Apache Curator (ZooKeeper recipes), JGroups.
- Most apps don't implement consensus — they *use* a consensus-backed system (ZooKeeper, etcd, Consul) for leader election and metadata.

```java
// Curator leader election sketch
LeaderSelector selector = new LeaderSelector(client, "/election", new LeaderSelectorListener() {
    @Override public void takeLeadership(CuratorFramework client) throws Exception {
        // I am the leader; do leader work until interrupted
    }
});
```

## 4. System design angle
- Quorum size: tolerate `(N-1)/2` failures. So 3 nodes tolerate 1, 5 tolerate 2.
- Latency: every commit needs a round trip to a majority → cross-region consensus is slow (~100ms).
- Don't put consensus on the hot path of every request — use it for *metadata* (who's leader, current config), then serve traffic from the leader directly.

## 5. Common mistakes / traps
- Using 2 or 4 nodes — even numbers don't help (still need majority, no fault tolerance gain).
- Running consensus across regions for primary write path → unbearable latency.
- Treating ZooKeeper/etcd as a general DB — they're slow for high write throughput.
- Not understanding that consensus tolerates only crashes, not Byzantine faults.

## 6. Revision checklist
- Why majority quorum matters: ______
- Raft's three phases: ______
- Why 3 nodes is better than 2: ______
- One real system built on Raft: ______
