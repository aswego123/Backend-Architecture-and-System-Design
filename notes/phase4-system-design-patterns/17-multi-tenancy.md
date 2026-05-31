# Multi-tenancy

> Phase 4 · Tags: `architecture` `saas`

## 1. The concept
Serving multiple customers (tenants) from one system. Isolation models, weakest → strongest:

1. **Shared everything (pool model)**: one DB, one schema, `tenant_id` column on every table. Cheapest, easiest to operate. Risk: a query missing the `tenant_id` filter leaks data.
2. **Shared DB, schema per tenant**: separates data; migrations × N schemas; still cheap.
3. **DB per tenant (silo)**: full isolation, easy compliance, expensive to operate (backups, upgrades × N).
4. **Cluster per tenant**: dedicated infra. Top-tier customers / strict compliance.

## 2. The rule / the why
Different tenants have different costs to serve, different compliance needs (HIPAA, residency), and different blast-radius tolerance. Multi-tenancy strategy decides cost, isolation, and complexity.

## 3. Java-specific behavior
- Hibernate has multi-tenant support (`MultiTenantConnectionProvider`, `CurrentTenantIdentifierResolver`) for schema- or DB-per-tenant.
- Pool model: enforce `tenant_id` in a base entity + Hibernate filter or row-level security in Postgres.
- Propagate tenant context through a `ThreadLocal` (or `ScopedValue` in Java 21+) populated by an auth filter.

## 4. System design angle
- **Noisy neighbor**: one heavy tenant slows others. Mitigate with per-tenant rate limits, query budgets, dedicated read replicas for big customers.
- Combine models: pool for free tier, silo for enterprise.
- Backups and restore-per-tenant are hardest in pool model — design exports up front.

## 5. Common mistakes / traps
- Forgetting `tenant_id` in a `WHERE` clause → cross-tenant leak (worst case).
- One huge tenant in pool DB → table size makes queries slow for everyone.
- Schema-per-tenant + thousands of tenants → migration nightmares, connection pool explosions.
- Mixing dev and prod tenants in the same store "temporarily".

## 6. Revision checklist
- Three isolation models: ______
- The pool model's #1 risk: ______
- One mitigation for noisy neighbors: ______
- Why DB-per-tenant doesn't scale to 10k customers: ______
