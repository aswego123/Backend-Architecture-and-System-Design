# Search engines (Elasticsearch)

> Phase 4 · Tags: `databases` `search`

## 1. The concept
A search engine indexes text using an **inverted index**: term → list of documents containing it.

Adds:
- **Analyzers**: tokenize, lowercase, stem, remove stop words.
- **Relevance scoring**: BM25 (Elasticsearch default) — combines term frequency, inverse document frequency, document length.
- **Aggregations**: facets, histograms, terms.
- **Vector search** (newer): kNN over dense embeddings for semantic search.

Elasticsearch and OpenSearch are the dominant general-purpose engines; Solr also popular. Both built on Lucene.

## 2. The rule / the why
RDB `LIKE '%foo%'` doesn't scale and doesn't rank. Real search needs tokenization, scoring, and an inverted index — purpose-built engines.

## 3. Java-specific behavior
- Elasticsearch Java client (`co.elastic.clients:elasticsearch-java`) — typed query DSL.
- Spring Data Elasticsearch for repository-style access (less flexible).
- Bulk API for indexing — never one-doc-at-a-time at scale.

## 4. System design angle
- ES is **not** your source of truth — index from the RDB via CDC or app dual-write (with outbox).
- Mapping (schema) decisions are mostly irreversible — reindex into a new mapping then alias-swap.
- Sharding: one primary shard per index → throughput cap. Pick shard count up front based on data size.
- Replica shards = read throughput + HA.

## 5. Common mistakes / traps
- Using default analyzer for non-English text → terrible relevance.
- Re-indexing the whole corpus when you could update incrementally.
- Treating ES as the system of record → no transactions, eventual consistency.
- Too many small indices → cluster metadata blows up.
- Index explosion from per-tenant indexes — use a `tenant_id` filter + shared index instead.

## 6. Revision checklist
- What an inverted index is: ______
- One reason `LIKE %foo%` doesn't replace search: ______
- ES should not be: ______
- One sharding constraint: ______
