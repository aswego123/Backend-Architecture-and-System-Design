# TLS & mTLS

> Phase 1 · Tags: `networking` `security`

## 1. The concept
**TLS** (Transport Layer Security) encrypts and authenticates a TCP connection. Handshake establishes:
1. Cipher suite agreement.
2. Server identity (X.509 certificate signed by a CA the client trusts).
3. A shared symmetric session key (via ECDHE — ephemeral, gives forward secrecy).

**mTLS**: both sides present certs. Server verifies client too.

TLS 1.3 cut the handshake to 1-RTT (and 0-RTT for resumed sessions).

## 2. The rule / the why
- **Confidentiality**: nobody on the wire can read your data.
- **Integrity**: nobody can tamper without detection.
- **Authentication**: you know you're talking to the real server.
- mTLS extends auth to the client — used inside service meshes to verify "is this caller really service-A?".

## 3. Java-specific behavior
- `SSLContext`, `KeyStore`, `TrustStore` (JKS or PKCS12).
- Default truststore: `$JAVA_HOME/lib/security/cacerts`.
- Common props: `-Djavax.net.ssl.trustStore=…`, `-Djavax.net.debug=ssl` for debugging.
- Spring Boot: configure via `server.ssl.*` and `server.ssl.client-auth=need` for mTLS.

## 4. System design angle
- TLS termination at the load balancer (CPU-cheap for backend) vs end-to-end (zero-trust networks).
- Service meshes (Istio, Linkerd) auto-issue short-lived certs and enforce mTLS between pods.
- CDN + edge TLS: clients see edge cert, origin sees CDN cert.

## 5. Common mistakes / traps
- Disabling certificate validation "to make it work" → MITM vulnerability.
- Expired certs taking down production (set up monitoring + auto-renewal via Let's Encrypt/ACME or cert-manager).
- Mixing protocols: TLS 1.0/1.1 are deprecated — disable them.
- Trusting hostname verification is on by default in every client — it isn't (some HTTP libs need explicit setup).

## 6. Revision checklist
- The three things TLS guarantees: ______
- What mTLS adds: ______
- Why ECDHE matters (forward secrecy): ______
- The #1 mistake (cert validation off): ______
