# Spring Security & JWT/OAuth2

> Phase 2 · Tags: `java` `spring` `security`

## 1. The concept
Spring Security is a **filter chain** in front of your app. Each filter does one job: authenticate, authorize, set security context, handle CSRF, etc.

- **Authentication (AuthN)**: who are you?
- **Authorization (AuthZ)**: are you allowed to do this?
- **OAuth2**: delegated authorization (token-based). Common flows: Authorization Code (with PKCE for public clients), Client Credentials (service-to-service).
- **OIDC**: identity layer on top of OAuth2 — adds `id_token` (JWT) describing the user.
- **JWT**: self-contained signed token. Pros: stateless. Cons: hard to revoke before expiry.

## 2. The rule / the why
Never roll your own auth. Spring Security handles edge cases (timing attacks, session fixation, CSRF) you'd forget.

## 3. Java-specific behavior
Modern config uses `SecurityFilterChain` bean (no more `WebSecurityConfigurerAdapter`):

```java
@Bean
SecurityFilterChain api(HttpSecurity http) throws Exception {
    return http
        .authorizeHttpRequests(a -> a
            .requestMatchers("/public/**").permitAll()
            .anyRequest().authenticated())
        .oauth2ResourceServer(o -> o.jwt(Customizer.withDefaults()))
        .csrf(c -> c.disable())   // for stateless APIs
        .sessionManagement(s -> s.sessionCreationPolicy(STATELESS))
        .build();
}
```

- Method-level: `@PreAuthorize("hasAuthority('SCOPE_admin')")`.
- For browser apps: keep CSRF enabled and use cookies. For pure APIs (mobile, SPA with bearer token): stateless + JWT.

## 4. System design angle
- API gateway validates the JWT once → passes claims downstream as headers.
- Short-lived access tokens (5–15 min) + refresh tokens limit blast radius of leaks.
- Service-to-service: mTLS or OAuth2 client credentials with scoped tokens.
- For revocation, either use short TTLs or maintain a token blacklist in Redis.

## 5. Common mistakes / traps
- Disabling CSRF on a cookie-authenticated browser app.
- Putting sensitive data in JWT — it's signed, not encrypted (anyone can decode the base64).
- Long-lived JWTs with no revocation strategy.
- Validating JWT signature but not `aud`/`iss`/`exp`.
- Symmetric (HS256) JWTs shared across services — every service holds the secret, huge blast radius. Use RS256/ES256.

## 6. Revision checklist
- AuthN vs AuthZ in one line each: ______
- The OAuth2 flow for an SPA: ______
- Why JWTs are signed but not (usually) encrypted: ______
- Two claims you must validate: ______
