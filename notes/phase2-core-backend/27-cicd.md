# CI/CD essentials

> Phase 2 · Tags: `devops`

## 1. The concept
- **CI (Continuous Integration)**: every push runs build + tests. Catches breakage early.
- **CD (Continuous Delivery)**: every green main build produces a deployable artifact. Deploy is one click.
- **Continuous Deployment**: green main build auto-deploys to prod.

Pipeline stages: checkout → build → test → static analysis → package (Docker image) → push to registry → deploy.

## 2. The rule / the why
Manual deploys are slow, error-prone, and gate-keepers of velocity. Automation makes small frequent releases safe — which lowers risk per change.

## 3. Java-specific behavior
A minimal GitHub Actions example:

```yaml
name: ci
on: [push, pull_request]
jobs:
  build:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      - uses: actions/setup-java@v4
        with: { distribution: temurin, java-version: '21', cache: gradle }
      - run: ./gradlew --no-daemon check
      - run: ./gradlew --no-daemon bootJar
      - uses: docker/build-push-action@v6
        with:
          tags: ghcr.io/me/app:${{ github.sha }}
          push: ${{ github.ref == 'refs/heads/main' }}
```

Add: SAST (CodeQL), dependency scan (OWASP DC, Snyk), license check, coverage report.

## 4. System design angle
- **Deployment strategies**: rolling, blue/green, canary. Canary lets you abort after the first 1% of traffic shows errors.
- **Feature flags** decouple deploy from release.
- **Immutable artifacts**: build once, promote the same image through environments.
- **DORA metrics**: deploy frequency, lead time, MTTR, change failure rate — the canonical health signals.

## 5. Common mistakes / traps
- Long-lived feature branches → painful merges, late integration.
- Flaky tests that everyone learns to ignore → real failures get missed.
- Rebuilding the artifact per environment → drift.
- No rollback plan ("just redeploy the previous version" without testing it).
- Secrets in plain text in workflow files.

## 6. Revision checklist
- CI vs CD vs Continuous Deployment: ______
- Why immutable artifacts matter: ______
- One safer deploy strategy: ______
- The "flaky test" rot: ______
