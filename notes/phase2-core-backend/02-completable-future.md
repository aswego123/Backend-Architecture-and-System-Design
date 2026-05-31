# CompletableFuture & async composition

> Phase 2 · Tags: `java` `concurrency` `async`

## 1. The concept
`CompletableFuture<T>` represents a value that will arrive later. You **compose** pipelines:
- `thenApply` — transform value.
- `thenCompose` — chain another async call (flatMap).
- `thenCombine` — combine two futures.
- `allOf` / `anyOf` — wait for many.
- `exceptionally` / `handle` — error recovery.

## 2. The rule / the why
Callback nesting is unreadable. `CompletableFuture` turns "callback hell" into a flat pipeline. You also get explicit control over which executor each stage runs on — important when mixing blocking and non-blocking work.

## 3. Java-specific behavior
- Default async stages run on `ForkJoinPool.commonPool()` — fine for CPU-bound, terrible for blocking I/O. Pass your own executor:

```java
CompletableFuture
    .supplyAsync(this::fetchUser, ioPool)
    .thenCombine(CompletableFuture.supplyAsync(this::fetchPrefs, ioPool),
                 (u, p) -> new UserView(u, p))
    .exceptionally(ex -> UserView.empty());
```

- With virtual threads, you often don't need CF at all — write straight-line blocking code on a virtual-thread executor.
- `join()` rethrows wrapped in `CompletionException`; `get()` declares `ExecutionException`.

## 4. System design angle
- Aggregation services that fan out to N microservices in parallel are the canonical CF use case.
- BFF (Backend-for-Frontend) layers stitch multiple downstream calls — CF or virtual threads + structured concurrency.

## 5. Common mistakes / traps
- Using the default common pool for blocking calls → starves every other CF user in the app.
- Forgetting that `thenApply` runs **on whatever thread completed the previous stage** — could be the caller.
- Swallowing exceptions: a CF that no one calls `join()` on can silently fail.
- Mixing CF with reactive (`Mono`/`Flux`) without bridging cleanly.

## 6. Revision checklist
- `thenApply` vs `thenCompose`: ______
- Why pass your own executor: ______
- One sign virtual threads make CF unnecessary: ______
