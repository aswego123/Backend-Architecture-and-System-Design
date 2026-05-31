# Monolith vs microservices

> Phase 4 · Tags: `architecture`

## 1. The concept
- **Monolith**: one deployable application; modules call each other in-process.
- **Modular monolith**: monolith with strict internal module boundaries — separate packages, clear interfaces, no cross-module DB tables. Often the right middle ground.
- **Microservices**: many independently deployable services, each with its own DB, talking over network (HTTP/gRPC/messaging).

## 2. The rule / the why
Microservices solve **organizational** problems (team autonomy, independent deploys, language polyglotism) at the cost of **operational and reasoning** complexity (network, partial failures, distributed tracing, data consistency).

Default to a modular monolith. Split only when you have a clear pain point: team coupling, deployment bottlenecks, scaling subsystems independently.

## 3. Java-specific behavior
- Modular monolith in Spring: enforce module boundaries with Spring Modulith or ArchUnit tests.
- Microservices: Spring Boot + Spring Cloud (or just plain HTTP/gRPC).
- Watch out: building "microservices" that share a database → distributed monolith (worst of both worlds).

## 4. System design angle
- Conway's Law: your architecture mirrors your org chart. Don't fight it — design around it.
- Bounded contexts (DDD) define service boundaries better than CRUD entities.
- Cross-cutting concerns (auth, observability, CI/CD) must mature *before* microservices, not after.
- "Just enough microservices" — most companies need 5–20, not 200.

## 5. Common mistakes / traps
- Splitting too early (no organizational pain yet) → drowning in YAML.
- Distributed monolith: many services, all calling each other synchronously, sharing DBs, deployed together.
- One database per service violated → schema coupling kills independence.
- No service catalog / ownership → orphaned services that nobody maintains.
- Skipping the modular monolith step.

## 6. Revision checklist
- Microservices solve what kind of problem: ______
- Modular monolith in one line: ______
- "Distributed monolith" smell: ______
- One pre-requisite before splitting: ______
