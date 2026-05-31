# NoSQL families

> Phase 4 · Tags: `databases` `nosql`

## 1. The concept
"NoSQL" = four very different families:

| Family | Examples | Shape | Use when |
|---|---|---|---|
| Key-Value | Redis, DynamoDB, RocksDB | `key → blob` | Sessions, caches, simple lookups |
| Document | MongoDB, Couchbase | `key → JSON tree` | Evolving schemas, nested objects, per-document workflows |
| Wide-column | Cassandra, ScyllaDB, HBase, Bigtable | `(row, col) → value`, sparse | Massive write throughput, time-series, IoT |
| Graph | Neo4j, JanusGraph, Neptune | Nodes + edges + properties | Relationships traversal (social, fraud rings, recommendations) |

## 2. The rule / the why
Pick the family that matches your **access pattern**, not the buzzword. Most apps need RDB + one NoSQL (cache or document), not five different stores.

## 3. Java-specific behavior
- Spring Data abstractions for each: `MongoRepository`, `RedisRepository`, etc.
- DynamoDB Enhanced Client (AWS SDK v2) for idiomatic Java mapping.
- Cassandra: design tables per query, not per entity (one query → one table). Use the driver's `QueryBuilder` or annotated entities.

## 4. System design angle
- Document DBs are great for write-then-read-the-whole-thing flows; bad for cross-document joins.
- Wide-column DBs require choosing the partition key carefully — bad keys = hot partitions.
- Graph DBs shine when queries are "find paths up to N hops"; otherwise an RDB with proper indexes is faster.
- NoSQL gives up SQL's flexibility — you commit to access patterns at design time.

## 5. Common mistakes / traps
- Choosing MongoDB because it's "easy" then needing transactions across documents.
- Cassandra with `ALLOW FILTERING` queries → table scan, terrible at scale.
- Treating Redis as a primary store without persistence config.
- Graph DB for problems that aren't graph-shaped → unnecessary complexity.
- Schemaless = no schema in code = chaos in 6 months. Define one in your app layer.

## 6. Revision checklist
- Four families and a one-liner use case each: ______
- Why "schemaless" doesn't mean "no schema": ______
- The Cassandra `ALLOW FILTERING` smell: ______
- One reason to stick with an RDB: ______
