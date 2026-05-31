# OWASP Top 10 for APIs

> Phase 4 · Tags: `security` `api`

## 1. The concept
The OWASP API Security Top 10 — the most common API vulnerabilities (2023 list, abridged):

1. **BOLA** (Broken Object Level Authorization): `GET /orders/123` returns someone else's order. Most common API bug.
2. **Broken Authentication**: weak password reset, no MFA, JWT misuse.
3. **Broken Object Property Level Authorization**: mass assignment (client sets `isAdmin=true`), excess data exposure (returning whole entity).
4. **Unrestricted Resource Consumption**: no rate limiting, no pagination caps → DoS.
5. **Broken Function Level Authorization**: `/admin/users` reachable by non-admins.
6. **Unrestricted Access to Sensitive Business Flows**: bots draining inventory, scraping.
7. **Server-Side Request Forgery (SSRF)**: app fetches a URL the attacker controls → internal network access.
8. **Security Misconfiguration**: default creds, verbose errors, missing headers (HSTS, CSP).
9. **Improper Inventory Management**: forgotten v1 endpoints still online.
10. **Unsafe Consumption of APIs**: trusting third-party API responses without validation.

## 2. The rule / the why
APIs are now the dominant attack surface. Knowing the canonical bug classes lets you prevent them by design.

## 3. Java-specific behavior
- BOLA: never trust the path/body for the owner — derive from authenticated principal, then `WHERE owner_id = :principalId`.
- Mass assignment: use DTOs, not entities, as `@RequestBody`. Never bind directly to JPA entities.
- Pagination caps: validate `size` parameter; reject > 100.
- SSRF: validate outbound URLs (allowlist hosts, block private IP ranges).
- Spring Boot 3 secure defaults are good, but verify: actuator secured, `server.error.include-message=never`.

## 4. System design angle
- Authorization at the resource layer, not just the route layer.
- API gateway can enforce schema validation, rate limits, and auth uniformly — defense in depth.
- Audit logs for sensitive operations (admin actions, exports).
- Threat modeling per feature (STRIDE) — catches design-level bugs before code.

## 5. Common mistakes / traps
- Trusting path IDs for AuthZ.
- Returning JPA entities → leaks fields.
- "Hidden" admin endpoints with no auth (security by obscurity).
- Verbose error messages leaking stack traces / SQL.
- Forgotten staging/v1 endpoints still routable in prod.

## 6. Revision checklist
- What is BOLA: ______
- Mass assignment defense: ______
- One SSRF mitigation: ______
- Why DTOs not entities: ______
