# Generics & type erasure

> Phase 1 · Tags: `java`

## 1. The concept
Generics give compile-time type safety: `List<String>` ensures only Strings go in. At runtime, the type parameter is **erased** to `Object` (or to the bound, for `<T extends Number>`) — the JVM doesn't know `T` exists.

Variance:
- `List<? extends Number>` — covariant; **producer** (can read Number, can't safely add).
- `List<? super Integer>` — contravariant; **consumer** (can add Integer, reads come out as Object).
- PECS: **P**roducer **E**xtends, **C**onsumer **S**uper.

## 2. The rule / the why
- Erasure existed for backward compatibility with pre-generics code (Java 1.4).
- Variance lets APIs accept the widest useful range of types without sacrificing safety.

## 3. Java-specific behavior
- You can't `new T()` or `T[]` or `instanceof T`.
- You **can** keep `Class<T>` token for runtime type access (`super type token` pattern in Jackson `TypeReference`).
- Raw types (`List` without `<>`) are legal but unsafe — compiler warns.
- Bridge methods are synthesized by `javac` to preserve polymorphism after erasure.

```java
static <T extends Comparable<? super T>> T max(Collection<? extends T> xs) { ... }
```

## 4. System design angle
- JSON/Protobuf libraries hit erasure constantly — Jackson uses `TypeReference<List<Foo>>() {}` (anonymous subclass keeps generic info via reflection on the superclass).
- Wildcards keep service interfaces flexible without forcing callers into rigid types.

## 5. Common mistakes / traps
- Forgetting PECS → API too restrictive or too permissive.
- Trying to `instanceof List<String>` → compile error; must use `List<?>`.
- Mixing raw and generic types → "unchecked" warnings that hide real `ClassCastException` later.
- Arrays + generics: `new T[10]` doesn't work; use `(T[]) new Object[10]` or `List<T>`.

## 6. Revision checklist
- Define erasure: ______
- PECS in one phrase: ______
- Why `new T()` is impossible: ______
- The super type token trick: ______
