# AuthN, AuthZ, OAuth2 flows

> Phase 4 · Tags: `security`

## 1. The concept
- **AuthN (authentication)**: prove who you are. Mechanisms: passwords + MFA, certificates, magic links, federated (SSO).
- **AuthZ (authorization)**: decide what you can do. Models: RBAC (roles), ABAC (attributes), ReBAC (relationships — e.g., Google Zanzibar).
- **OAuth2**: a delegation protocol — let app X act on user Y's resources at provider Z, without giving X the password.
- **OIDC**: identity on top of OAuth2 — adds `id_token` with user identity claims.
- **SSO**: one login → access to multiple apps. Usually via SAML or OIDC.

OAuth2 flows:
- **Authorization Code + PKCE**: SPAs and mobile (public clients).
- **Authorization Code + client secret**: confidential server-side apps.
- **Client Credentials**: service-to-service (no user).
- **Device Code**: TVs, CLIs.
- (Avoid Implicit and Password — deprecated.)

## 2. The rule / the why
Don't roll your own auth. Use an identity provider (Auth0, Keycloak, Okta, AWS Cognito, Google). Security is a moving target you don't want to chase.

## 3. Java-specific behavior
- Spring Security OAuth2 Resource Server (validate JWTs) + OAuth2 Client (talk to providers).
- Method-level `@PreAuthorize("hasAuthority('SCOPE_orders.read') and #orderId == authentication.principal.orderId")`.
- For ReBAC, look at SpiceDB or Permify.

## 4. System design angle
- API gateway validates tokens once; passes claims downstream as headers.
- Tokens: short-lived access (5–15 min) + refresh (longer, rotatable).
- Service-to-service: mTLS or client-credentials JWTs with audience checks.
- AuthZ enforcement must be at the service that owns the resource — never trust the client.

## 5. Common mistakes / traps
- Mixing AuthN identity (who) with AuthZ decisions (what). Token says "user is X with scopes Y" — server decides if Y allows the action.
- AuthZ enforced only in the UI → APIs are wide open.
- Long-lived tokens with no revocation strategy.
- Letting the JWT's claims drift from reality (user removed from team, but token still says they're a member).
- Roles that explode into hundreds — model with attributes/relationships instead.

## 6. Revision checklist
- AuthN vs AuthZ one-liner each: ______
- RBAC vs ABAC vs ReBAC: ______
- Best OAuth2 flow for an SPA: ______
- The "UI-only AuthZ" anti-pattern: ______
