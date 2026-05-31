# Encryption: at rest, in transit

> Phase 4 · Tags: `security`

## 1. The concept
- **In transit**: TLS for all network traffic (client→edge, edge→service, service→DB).
- **At rest**: data encrypted on disk. DB-level (TDE), volume-level (LUKS, EBS encryption), application-level (encrypt specific columns).
- **Envelope encryption**: data encrypted with a **DEK** (data encryption key); the DEK is itself encrypted with a **KEK** (key encryption key) stored in a KMS. Rotate KEK without re-encrypting all data.
- **Symmetric** (AES-GCM) for bulk data; **asymmetric** (RSA, ECDSA) for key exchange and signatures.

## 2. The rule / the why
Encryption protects against different threats:
- TLS → eavesdroppers on the network.
- At-rest → stolen disks, snapshot leaks.
- Application-level → operators/DBAs reading the DB.

Encryption is not a substitute for access control or input validation; it's a layer.

## 3. Java-specific behavior
- TLS via `SSLContext`; default cipher suites in modern JDKs are fine — don't downgrade.
- For app-level encryption: AWS Encryption SDK, Google Tink. **Don't roll your own crypto** — use a library.
- JCE unlimited-strength is on by default in Java 9+.

## 4. System design angle
- Cloud KMS (AWS KMS, GCP KMS, Vault Transit) lets your app encrypt/decrypt without ever seeing the master key. Audit logs come for free.
- Field-level encryption for PII (SSN, payment data) → reduces compliance scope.
- Key rotation: KMS supports automatic rotation of KEKs; DEKs rotate when you re-encrypt data.
- Backup encryption: always-on.

## 5. Common mistakes / traps
- TLS 1.0/1.1 enabled — disable; use TLS 1.2+ (1.3 preferred).
- Self-signed certs in production "to skip CA setup".
- Storing encryption keys next to encrypted data ("encryption" with no security gain).
- ECB mode (don't use; produces patterns). Use AES-GCM.
- Logging decrypted PII → bypasses encryption entirely.

## 6. Revision checklist
- Three places encryption protects you: ______
- Envelope encryption purpose: ______
- One mode to avoid: ______
- Why not roll your own crypto: ______
