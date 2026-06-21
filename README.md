# Backend Architecture & System Design — Learning Repo

A learning-first repo for backend engineering (Java) and system design. Every topic note follows the same 6-section format so revision is fast and predictable.

## Start here
1. Read [LEARNING_MAP.md](LEARNING_MAP.md) — the full roadmap (5 phases, essential vs optional).
2. Open [TOPIC_TEMPLATE.md](TOPIC_TEMPLATE.md) — the format every note uses.
3. Pick one topic from [notes/](notes/) and read it actively (write the "why" in your own words first).
4. Pick a [practice/](practice/) project and write its design doc *before* coding.

## Repo layout
```
LEARNING_MAP.md         # the roadmap
TOPIC_TEMPLATE.md       # note format
notes/
  phase1-foundations/
  phase2-core-backend/
  phase3-distributed-systems/
  phase4-system-design-patterns/
  phase5-advanced/
practice/
  00-how-to-do-projects.md
  01-url-shortener/ ... 07-mini-kafka/
```

## How to learn from this repo
- **One topic at a time.** Depth beats breadth.
- **Write before you read.** Try to answer "what problem does this solve?" in your own words first.
- **Build the smallest thing.** Every abstract concept gets a tiny Java prototype.
- **Revise weekly.** Use section 6 of each note as a flash-test.

## Conventions
- Java 21+ (records, virtual threads, pattern matching) where they help.
- Spring Boot 3.x for framework examples.
- PostgreSQL as default RDB, Redis as default cache, Kafka as default broker.

## Reference shelf
- *Designing Data-Intensive Applications* — Martin Kleppmann
- *System Design Interview Vol. 1 & 2* — Alex Xu
- ByteByteGo (newsletter + YouTube)
- High Scalability blog; engineering blogs of Uber, Netflix, Dropbox, Discord
- roadmap.sh/backend

## More Reference Links
- https://www.youtube.com/watch?v=C842vFY5kRo
- https://www.youtube.com/watch?v=Qa-7iWxDz1A&t=53s
- https://www.youtube.com/watch?v=s9Qh9fWeOAk
