# JUnit 5, Mockito, AssertJ

> Phase 2 · Tags: `testing` `java`

## 1. The concept
The standard Java test stack:
- **JUnit 5 (Jupiter)**: test runner. `@Test`, `@BeforeEach`, `@ParameterizedTest`, `@Nested`.
- **Mockito**: mocks/stubs for collaborators. `mock()`, `when().thenReturn()`, `verify()`.
- **AssertJ**: fluent, readable assertions. `assertThat(x).isEqualTo(...)`.

## 2. The rule / the why
- Tests are the executable spec; treat them as production code.
- Mockito isolates the unit under test from collaborators.
- AssertJ produces failure messages worth reading (JUnit's defaults are bare).

## 3. Java-specific behavior

```java
@Test
void chargesUser() {
    var payments = mock(PaymentClient.class);
    when(payments.charge(any())).thenReturn(Receipt.ok("r1"));

    var service = new OrderService(payments);
    var result = service.place(new Order(...));

    assertThat(result.status()).isEqualTo(PAID);
    verify(payments).charge(argThat(c -> c.amount().equals(BigDecimal.TEN)));
}
```

- `@ParameterizedTest` + `@CsvSource` / `@MethodSource` covers many cases without duplication.
- `@SpringBootTest` for full-context integration tests (slow); `@WebMvcTest`, `@DataJpaTest` for slices (fast).
- Mockito strict mode catches stubs that were never used.

## 4. System design angle
- Test pyramid: many fast unit tests, fewer integration tests, very few end-to-end.
- Slice tests (`@WebMvcTest`) keep the cycle fast.
- Property-based testing (`jqwik`) finds edge cases unit tests miss.

## 5. Common mistakes / traps
- Mocking what you don't own (third-party HTTP clients) → brittle; use Testcontainers or WireMock for real behavior.
- Asserting on internals (private state) instead of observable behavior.
- One giant `@SpringBootTest` for everything → slow suite, weak feedback.
- `verify(mock, times(1))` everywhere — over-specifies; verify only what matters.
- `Thread.sleep(...)` in tests instead of `Awaitility` or fakes.

## 6. Revision checklist
- Roles of JUnit / Mockito / AssertJ in one sentence each: ______
- Slice test vs full context test: ______
- One mocking anti-pattern: ______
- The test pyramid in one line: ______
