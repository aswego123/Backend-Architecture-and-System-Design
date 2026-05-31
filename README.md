Phase 1 — Foundations first
Don't skip this. System design is built on networking (HTTP, TCP/IP, DNS), databases (indexing, transactions, ACID), OS concepts (processes, threads, I/O), and data structures. Most gaps in system design knowledge trace back here.

Phase 2 — Core tech stack
Pick one backend language and go deep (Go and Python are the most popular for backend right now). Learn to build REST and gRPC APIs, understand a relational DB (PostgreSQL) deeply, and get comfortable with Redis for caching. Add Docker and basic cloud (AWS/GCP) so you can actually deploy what you build.
Phase 3 — System design concepts
This is the meat of it. Study these topics in roughly this order:

Scalability patterns (horizontal scaling, stateless services)
Caching strategies (CDN, write-through vs lazy loading)
Load balancing (consistent hashing is key)
Message queues and async patterns (Kafka is the industry standard)
Database deep dives (sharding, replication, CAP theorem)
Microservices patterns (API gateway, service mesh, circuit breakers)
Observability (metrics, logs, distributed tracing)

Phase 4 — Practice
Read engineering blogs from Uber, Airbnb, Dropbox, and Netflix — they explain real decisions at scale. Build classic projects: a URL shortener, a rate limiter, a Twitter clone. Do mock system design interviews on paper or Excalidraw to simulate the pressure.
Best resources to go deep:

Designing Data-Intensive Applications by Martin Kleppmann — the definitive book, read it slowly
ByteByteGo (Alex Xu's newsletter + YouTube) — best visual explanations of patterns
System Design Interview Vol. 1 & 2 by Alex Xu — practical interview prep
High Scalability blog and the individual engineering blogs of major companies
roadmap.sh/backend — a free interactive checklist of everything to learn