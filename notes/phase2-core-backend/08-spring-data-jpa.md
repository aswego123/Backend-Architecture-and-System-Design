# Spring Data JPA & the N+1 trap

> Phase 2 · Tags: `java` `spring` `databases`

## 1. The concept
Spring Data JPA = Spring wiring around JPA (Java Persistence API), with Hibernate as the usual implementation.

You declare repository interfaces; Spring synthesizes implementations:

```java
public interface UserRepository extends JpaRepository<User, Long> {
    Optional<User> findByEmail(String email);
    @Query("select u from User u left join fetch u.orders where u.id = :id")
    Optional<User> findByIdWithOrders(@Param("id") Long id);
}
```

## 2. The rule / the why
ORM removes hand-written SQL boilerplate and maps between objects and tables. The price: a leaky abstraction. You *must* understand the SQL it generates, or performance and correctness both suffer.

## 3. Java-specific behavior
- Entity lifecycle: transient → managed → detached → removed.
- The **persistence context** (1st-level cache) is per transaction.
- Lazy fetching is the default for `@OneToMany` / `@ManyToMany`. Accessing the field outside a transaction throws `LazyInitializationException`.
- `@Transactional` boundaries control flush, rollback, and persistence context lifetime.
- Use `@EntityGraph` or `JOIN FETCH` to fix N+1 cleanly.

## 4. System design angle
- For complex read queries, drop JPA and use JDBC/jOOQ — don't fight the ORM.
- Read-heavy services often pair JPA writes with separate read models (CQRS-lite).
- Schema migrations via Flyway or Liquibase, version-controlled in the repo.

## 5. Common mistakes / traps (the famous ones)
- **N+1 queries**: load N parents, then trigger N child queries via lazy fields. Fix with `@EntityGraph`, `JOIN FETCH`, or `@BatchSize`.
- `OpenSessionInView` enabled in production — masks N+1 by letting lazy loads succeed outside service layer.
- Treating entities as DTOs and returning them from controllers → leaks DB schema, triggers lazy loads at serialization time.
- Long-running transactions holding DB connections.
- Bidirectional relationships without keeping both sides in sync.

## 6. Revision checklist
- Persistence context = ______
- N+1 in one sentence, plus one fix: ______
- Why returning entities from controllers is bad: ______
- One reason to bypass JPA for a query: ______
