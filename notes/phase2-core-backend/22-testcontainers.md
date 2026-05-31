# Testcontainers

> Phase 2 · Tags: `testing` `java` `docker`

## 1. The concept
Testcontainers spins up real services (Postgres, Redis, Kafka, Elasticsearch, …) in Docker containers for the duration of your tests. You get real-DB behavior without hand-managed test environments.

## 2. The rule / the why
- H2 / in-memory DBs lie. Their dialect differs from Postgres/MySQL → bugs only appear in prod.
- Real containers = real SQL, real isolation, real performance characteristics.
- Cost: a few seconds startup per container (mitigated by reuse).

## 3. Java-specific behavior

```java
@Testcontainers
class UserRepoIT {
    @Container
    static PostgreSQLContainer<?> pg = new PostgreSQLContainer<>("postgres:16");

    @DynamicPropertySource
    static void props(DynamicPropertyRegistry r) {
        r.add("spring.datasource.url", pg::getJdbcUrl);
        r.add("spring.datasource.username", pg::getUsername);
        r.add("spring.datasource.password", pg::getPassword);
    }
}
```

- Use `@ServiceConnection` (Spring Boot 3.1+) to skip the property wiring boilerplate.
- Reusable containers: `.withReuse(true)` + `testcontainers.reuse.enable=true` keeps them across local runs.

## 4. System design angle
- Run the same DB version in tests as in prod → catches dialect/migration bugs.
- Compose multiple containers (app + DB + Kafka) for end-to-end tests.
- CI: ensure Docker-in-Docker or a Docker socket is available.

## 5. Common mistakes / traps
- One container per test class without reuse → slow suite.
- Not pinning image versions (`postgres:latest`) → flaky builds.
- Sharing state across tests with no cleanup → order-dependent flakes.
- Forgetting Docker isn't available in some CI sandboxes — have a fallback path.

## 6. Revision checklist
- Why prefer Testcontainers over H2: ______
- One feature that speeds up the test suite: ______
- One CI concern when adopting it: ______
