# Secrets management

> Phase 4 · Tags: `security`

## 1. The concept
Secrets = credentials, API keys, DB passwords, signing keys. They must never live in source control, container images, or plain env vars on shared hosts.

Secret stores: HashiCorp Vault, AWS Secrets Manager, AWS KMS, GCP Secret Manager, Kubernetes Secrets (with encryption at rest enabled).

Patterns:
- **Static secret**: stored once, fetched at app start.
- **Dynamic secret**: generated on demand with short TTL (Vault can issue per-app DB credentials).
- **Workload identity**: app authenticates to cloud via metadata service (no static credentials at all).

## 2. The rule / the why
Leaked secrets are the most common breach root cause. Centralizing storage gives audit logs, rotation, and least-privilege access.

## 3. Java-specific behavior
- Spring Cloud Vault, AWS Secrets Manager starter, etc., fetch secrets at startup or on demand.
- `@RefreshScope` to rebind on secret rotation.
- Don't log secret values, even in DEBUG. Mask in toString().

## 4. System design angle
- Rotation: rotate periodically and on incident. Dynamic secrets sidestep rotation pain.
- Encryption at rest: K8s Secrets are base64-encoded by default (not encrypted) — enable encryption-at-rest provider.
- Least privilege: each service gets only the secrets it needs.
- Audit: every secret access logged + alertable.

## 5. Common mistakes / traps
- Secrets in `application.yml` checked into git (use `git-secrets`, `gitleaks` to scan).
- Secrets in Docker `ENV` directives — visible in `docker inspect` and image layers.
- Long-lived static credentials in CI workflows — use OIDC federation instead.
- Sharing one DB user across many services — can't tell who did what.

## 6. Revision checklist
- Two places never to store secrets: ______
- Dynamic vs static secrets: ______
- One way to avoid static credentials entirely: ______
- Why rotation matters even without a breach: ______
