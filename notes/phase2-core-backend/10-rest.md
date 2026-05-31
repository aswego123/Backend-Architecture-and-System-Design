# REST design done right

> Phase 2 · Tags: `api` `http`

## 1. The concept
REST = use HTTP semantics to model resources. URLs identify resources (nouns), verbs say what to do.

| Verb | Use | Idempotent? | Safe? |
|---|---|---|---|
| GET | Read | Yes | Yes |
| POST | Create or "do action" | No | No |
| PUT | Replace entire resource | Yes | No |
| PATCH | Partial update | Usually yes | No |
| DELETE | Remove | Yes | No |

Idempotent = repeating it has the same effect. Critical for retries.

## 2. The rule / the why
HTTP gives you caching, status codes, conditional requests, content negotiation for free — *if* you follow the conventions. Diverging (e.g., POST for everything) discards those benefits.

## 3. Java-specific behavior
- Spring MVC: `@RestController`, `@GetMapping`, `@PostMapping`, `ResponseEntity<T>`.
- Validation: `@Valid @RequestBody`, `jakarta.validation` constraints.
- Pagination: accept `?page=&size=&sort=` (Spring `Pageable`); return `{ content, page, totalElements }`.
- Errors: use RFC 7807 (`application/problem+json`) — Spring Boot 3 supports it natively.

```java
@PostMapping("/orders")
public ResponseEntity<OrderDto> create(@Valid @RequestBody CreateOrderDto req) {
    var saved = service.create(req);
    return ResponseEntity.created(URI.create("/orders/" + saved.id())).body(saved);
}
```

## 4. System design angle
- Status codes that matter: 200, 201 (Created + Location header), 202 (Accepted, async), 204 (No Content), 400, 401, 403, 404, 409 (Conflict), 422 (Validation), 429 (Rate-limited), 5xx.
- Versioning: prefer URL versioning (`/v1/...`) for simplicity. Header versioning is purer but harder to debug.
- Idempotency keys (`Idempotency-Key` header) for safe retries of POST.
- Pagination: cursor-based for large/changing datasets, offset for small static ones.

## 5. Common mistakes / traps
- Returning 200 with `{"error": ...}` instead of a 4xx/5xx status.
- Verbs in URLs (`/getUser/123`) — use `GET /users/123`.
- Inconsistent error formats across endpoints.
- Breaking changes without a new version.
- Returning JPA entities directly — leaks schema and lazy fields.

## 6. Revision checklist
- Define idempotent: ______
- Verb choice for "delete user": ______
- Status code for "you sent valid JSON but the value is wrong": ______
- One reason cursor > offset pagination: ______
