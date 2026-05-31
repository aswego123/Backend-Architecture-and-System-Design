# Service discovery & config

> Phase 4 · Tags: `infra`

## 1. The concept
- **Service discovery**: how do services find each other when IPs are ephemeral (containers/pods come and go)?
  - **Client-side**: client queries a registry (Eureka, Consul) and load-balances itself.
  - **Server-side**: client calls a logical name; LB/proxy/DNS resolves it (Kubernetes Service, Envoy).
- **Configuration management**: where do services get their config (DB URLs, feature flags) without baking it into the binary?

## 2. The rule / the why
Hardcoded IPs/URLs break the moment you scale or redeploy. Centralized config lets you change behavior without redeploying.

## 3. Java-specific behavior
- Eureka + Spring Cloud Netflix (legacy but works).
- Kubernetes-native: services are auto-discovered by DNS — `http://user-service.default.svc.cluster.local`.
- Spring Cloud Config / Consul KV / AWS AppConfig for centralized config.
- `@RefreshScope` rebinds config without restart.

## 4. System design angle
- Server-side discovery (K8s) is simpler — let the platform do it.
- Config sources: defaults < file < env var < remote config service < runtime override.
- **Feature flags** (LaunchDarkly, Unleash, ConfigCat) are dynamic config for behavior switches — decouples deploy from release.
- Secrets are a different beast — use Vault / cloud KMS, not the same store as config.

## 5. Common mistakes / traps
- Polling a config service on every request → latency + dependency.
- Config updates that aren't safe to apply live (e.g., changing thread pool size mid-request).
- Feature flags accumulating with no cleanup → branching nightmare.
- Service discovery becomes single point of failure — make it HA.

## 6. Revision checklist
- Client-side vs server-side discovery: ______
- One source of config beyond env vars: ______
- Feature flags solve what problem: ______
- The "polling on every request" anti-pattern: ______
