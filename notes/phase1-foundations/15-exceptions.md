# Exceptions: checked vs unchecked

> Phase 1 · Tags: `java`

## 1. The concept
- **Checked** (`extends Exception`): compiler forces `throws` or `try/catch`. Example: `IOException`.
- **Unchecked** (`extends RuntimeException`): no compile-time enforcement. Example: `NullPointerException`, `IllegalArgumentException`.
- **Error** (`extends Error`): JVM-level (OOM, StackOverflow) — don't catch.

## 2. The rule / the why
Checked exceptions were meant to force handling of recoverable failures. In practice they leak into every method signature, couple callers to callees, and clash with lambdas (which can't throw checked).

Modern Java style: use unchecked exceptions for almost everything; reserve checked only for genuine recoverable errors at API boundaries (and even then, many libraries don't).

## 3. Java-specific behavior
- `try-with-resources` auto-closes anything implementing `AutoCloseable`.
- Suppressed exceptions: when close() throws while another exception is in flight, it attaches via `addSuppressed`.
- Lambdas (`Function`, `Consumer`) declare no checked exceptions → wrap in `RuntimeException` or use libraries (`Vavr`, `jOOλ`).
- `Optional` is the idiomatic replacement for "return null on absent".

```java
try (var in = Files.newInputStream(path)) {
    return in.readAllBytes();
} // auto-close, even on exception
```

## 4. System design angle
- At service boundaries (REST, gRPC), translate exceptions into stable error codes — don't leak stack traces.
- Use Spring's `@ControllerAdvice` for centralized exception → HTTP status mapping.
- Distinguish **client errors** (4xx — bad input, don't retry) from **server errors** (5xx — may retry).

## 5. Common mistakes / traps
- `catch (Exception e) { /* swallow */ }` — hides bugs; at minimum log it.
- Catching `Throwable` — catches `Error` too; almost never right.
- Throwing `RuntimeException` with no message — debugging nightmare.
- Using exceptions for control flow (e.g., parse-by-try) — slow due to stack capture.
- Returning null instead of throwing or returning `Optional`.

## 6. Revision checklist
- Checked vs unchecked one-liner: ______
- Why modern style avoids checked: ______
- `try-with-resources` solves what: ______
- One anti-pattern: ______
