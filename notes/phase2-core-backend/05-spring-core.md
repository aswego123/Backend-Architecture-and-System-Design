# Spring Core: IoC & beans

> Phase 2 · Tags: `java` `spring`

## 1. The concept
**Inversion of Control (IoC)**: instead of `new`-ing collaborators, you declare what you need and the container hands it to you.

**Bean**: an object managed by the Spring container. It knows the bean's class, scope, lifecycle, and dependencies.

**Dependency Injection (DI)**: the mechanism Spring uses to wire beans. Constructor injection is preferred.

Scopes: `singleton` (default), `prototype`, `request`, `session`.

## 2. The rule / the why
- Decouples *what* a class needs from *who* provides it.
- Makes testing easy — swap a real `UserRepository` for a mock without touching the consuming class.
- Centralizes configuration; lifecycle (init/destroy) becomes the framework's problem.

## 3. Java-specific behavior
- Annotations: `@Component`, `@Service`, `@Repository`, `@Controller`, `@Configuration`, `@Bean`.
- Prefer constructor injection (immutable, easy to test, fails fast on missing deps):

```java
@Service
public class OrderService {
    private final PaymentClient payments;
    public OrderService(PaymentClient payments) { this.payments = payments; }
}
```

- `@Primary`, `@Qualifier`, `@ConditionalOn*` for picking among multiple candidates.
- Lifecycle hooks: `@PostConstruct`, `@PreDestroy`, `InitializingBean`, `DisposableBean`.

## 4. System design angle
- Profiles (`@Profile("prod")`) allow per-environment wiring (in-memory vs real DB in tests).
- `@Configuration` classes act as composition roots — keep wiring explicit and reviewable.

## 5. Common mistakes / traps
- Field injection (`@Autowired` on a field) — hides dependencies, breaks immutability, hard to test.
- Circular dependencies — Spring will usually fail or proxy them awkwardly; refactor instead.
- Putting business logic in `@Configuration` classes.
- Forgetting that `singleton` beans must be thread-safe (almost always shared across requests).

## 6. Revision checklist
- IoC in one line: ______
- Why constructor injection beats field injection: ______
- Default scope and its threading implication: ______
- One way to swap impls per environment: ______
