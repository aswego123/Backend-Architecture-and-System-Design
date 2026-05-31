# OpenAPI / contract-first

> Phase 2 · Tags: `api`

## 1. The concept
**OpenAPI** (formerly Swagger) is a YAML/JSON spec describing your HTTP API: paths, methods, schemas, auth.

**Contract-first**: write the OpenAPI spec, then generate server stubs and client SDKs from it. Opposite of code-first (write controllers, generate spec from them).

## 2. The rule / the why
- The contract is the source of truth → frontends and backends develop in parallel against the same spec.
- Generated clients eliminate hand-written HTTP code and drift.
- Mock servers (Prism, Stoplight) let consumers test before the real API exists.

## 3. Java-specific behavior
- Code-first: `springdoc-openapi` auto-generates spec from annotations (`@Operation`, `@Schema`).
- Contract-first: `openapi-generator-maven-plugin` generates interfaces from a YAML spec; you implement them.
- Both produce a Swagger UI at `/swagger-ui.html` by default.

## 4. System design angle
- Spec lives in a shared repo or registry → consumers pull versioned SDKs.
- API gateway can enforce the spec (request validation) — rejects malformed input before it reaches your service.
- Breaking-change detection in CI (e.g., `openapi-diff`) prevents accidental contract violations.

## 5. Common mistakes / traps
- Code-first specs that drift from reality because devs don't update annotations.
- No examples in the spec — consumers guess shape.
- Forgetting to document error responses — clients only see the happy path.
- Generating clients into the producer's repo (coupling consumers).

## 6. Revision checklist
- Contract-first vs code-first in one line: ______
- Two things you get from generated clients: ______
- One CI check to add for API stability: ______
