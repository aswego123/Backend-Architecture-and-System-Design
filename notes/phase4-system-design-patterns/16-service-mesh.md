# Sidecars & service mesh

> Phase 4 · Tags: `infra` `kubernetes`

## 1. The concept
- **Sidecar**: a helper container deployed alongside your app container, sharing the network namespace. Handles cross-cutting concerns transparently (proxy, log shipper, secrets injector).
- **Service mesh** (Istio, Linkerd, Consul Connect): a network of sidecar proxies (usually Envoy) + a control plane. Provides mTLS, retries, traffic shifting, observability, and authz — without app code changes.

## 2. The rule / the why
- Push cross-cutting networking concerns out of every app codebase. Polyglot teams get the same features for free.
- Cost: extra latency per hop, operational complexity, debugging gets harder.

## 3. Java-specific behavior
- With a mesh, you remove Resilience4j-style retries/CB from app code — the mesh does it. You still use them for in-app dependencies (DB, cache).
- mTLS is provided by the mesh, not your app. Don't double-encrypt.
- Tracing headers still need to propagate in the app (mesh injects spans for hops, but in-app spans are yours).

## 4. System design angle
- Adopt a mesh when you have **many** services, polyglot, and need uniform policy (security, retry, observability).
- For a 5-service Spring app, the mesh is overkill — use Resilience4j + OpenTelemetry directly.
- Sidecar resource cost is real: ~100 MB RAM, some CPU per pod.

## 5. Common mistakes / traps
- Deploying a mesh "for the future" before you need it — debugging becomes harder for no payoff.
- Mesh retries + app retries → multiplicative retry storms.
- Trusting mesh mTLS but exposing the pod port directly inside the cluster.
- Ignoring sidecar startup ordering — app starts before sidecar is ready → first requests fail.

## 6. Revision checklist
- Sidecar vs service mesh: ______
- Two features a mesh provides: ______
- One reason not to adopt one: ______
- The retry duplication risk: ______
