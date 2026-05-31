# Geo-distributed systems

> Phase 5 · Tags: `distributed`

## 1. The concept
Running a system across multiple regions for low latency to global users, disaster resilience, or data sovereignty.

Topologies:
- **Active-passive**: one writer region; others are standby. Failover is manual or semi-automatic.
- **Active-active read**: all regions read locally; writes go to one. Simple, common.
- **Active-active write**: all regions write locally. Needs conflict resolution (CRDTs, last-write-wins, sharding by region).
- **Globally consistent DBs**: Spanner, CockroachDB, YugabyteDB — multi-region strong consistency at the cost of write latency (~100ms+ across continents).

## 2. The rule / the why
Speed of light is the budget. SF ↔ Frankfurt = ~85ms one-way at best. Cross-region strong consistency means every write pays that cost.

## 3. Java-specific behavior
- Route reads to the nearest replica via DNS (geo-routing) or smart client.
- Spring + multi-datasource for region-aware writes.
- Use Kafka MirrorMaker for cross-region event replication.

## 4. System design angle
- Shard by region when possible (`user_id` mod region) → most users write locally.
- Multi-region active-active is hard. Conflict resolution is hard. Don't adopt unless the business requires it.
- Compliance: data residency (GDPR, India DPDPA) often *forces* multi-region with strict boundaries — design schemas with `region` as a partition key.

## 5. Common mistakes / traps
- "Active-active" advertised but writes synchronously cross regions → unbearable p99.
- Multi-master without conflict resolution → silent divergence.
- Forgetting that a region outage means failing over *all* services that depend on its data.

## 6. Revision checklist
- One latency budget across an ocean: ______
- Why active-active write is hard: ______
- One reason geo-distribution is required: ______
