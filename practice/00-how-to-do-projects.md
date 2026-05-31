# How to do these projects

The goal isn't a working app — it's the **reasoning**. A tutorial-followed project teaches you nothing you'll remember in 3 months. A project you designed first, then built, sticks.

## The design-doc-first method

For every project, before touching code, fill in the design doc in this order:

### 1. Requirements
- Functional: what does it do? (e.g., "shorten a URL", "send a message").
- Non-functional: throughput, latency, durability, consistency. Pick **numbers**.
- Out of scope: be explicit.

### 2. Capacity estimate
Back-of-the-envelope:
- QPS (peak vs average).
- Storage growth per year.
- Read/write ratio.
- Bandwidth.

These numbers force real architectural decisions instead of "we'll add a cache somewhere".

### 3. API
Sketch the endpoints (or events). One line each: method, path, request, response. No code yet.

### 4. Data model
Tables/collections/topics. Keys, indexes, partitioning. Why this shape?

### 5. High-level architecture
A box-and-arrow diagram (use Mermaid in the doc):
- Clients → LB → service(s) → DB / cache / broker / storage.
- Where each cross-cutting concern lives (auth, rate limit, observability).

### 6. Deep dives
Pick the 2–3 hardest sub-problems and design them in detail. Examples:
- "How do we generate short codes without collisions at 10k QPS?"
- "How do we deliver a message exactly once to a user with 5 devices?"

### 7. Failure modes
For each component: what happens when it dies? How do we detect it? Recover?

### 8. Trade-offs
Decisions you made and what you gave up. Alternatives you rejected, and why.

## Then — and only then — build

- Pick the **smallest** runnable slice. End-to-end vertical, not horizontal layers.
- Use Spring Boot 3 + Postgres + (Redis or Kafka if relevant) by default.
- Tests from day one with Testcontainers — production-shaped deps locally.
- Add load testing (k6, Gatling) early. Compare actual numbers to your estimate.
- Write a **postmortem** after: what was wrong in the design doc? Update the doc.

## Anti-patterns

- Following a tutorial line-by-line.
- Adding microservices because the project sounds "system design-y". Start with a monolith.
- Skipping the capacity estimate → over- or under-engineering.
- Building all CRUD endpoints before the hard part.
- No load test → "it scales" is a guess.

## When to revisit

After Phases 1–2: do projects 1–3.
After Phase 3: do projects 4–6.
After Phase 4: do project 7. Then re-design project 1 from scratch and notice how much better it is.
