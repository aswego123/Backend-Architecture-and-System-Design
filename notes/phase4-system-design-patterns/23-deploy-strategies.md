# Deploy strategies & feature flags

> Phase 4 · Tags: `devops` `reliability`

## 1. The concept
- **Rolling**: replace instances N at a time. Default in Kubernetes.
- **Blue/green**: two full environments; flip traffic at once. Easy rollback by flipping back.
- **Canary**: route a small % to the new version; widen if healthy. Best mix of safety + speed.
- **Shadow**: send a copy of real traffic to the new version; observe but don't return responses.
- **Feature flag**: ship code dark; enable per user/cohort at runtime. Decouples deploy from release.

## 2. The rule / the why
The risk of a deploy is concentrated in the moment it goes live. Progressive strategies + flags spread the risk over time and let you abort early.

## 3. Java-specific behavior
- K8s `Deployment` does rolling out of the box.
- Argo Rollouts / Flagger for canary + automatic analysis.
- Feature flags: LaunchDarkly, Unleash (OSS), Flagsmith, or a small home-grown system reading from a config service.
- Spring `@ConditionalOnProperty` for compile-time toggles; runtime flags need a client lib.

## 4. System design angle
- Always have a working rollback path — automated, tested, < 5 min.
- DB migrations must be backward-compatible across (current, new) versions. Use expand-contract: add column → backfill → start writing both → switch reads → drop old.
- Stateful services (DBs) are hard to canary — version the schema, not the code, when possible.
- Feature flags double as kill switches for new features in incidents.

## 5. Common mistakes / traps
- "Big bang" releases on Friday afternoon.
- DB migration coupled to app deploy → can't roll back without data loss.
- Feature flags piling up without cleanup → tangled conditionals everywhere.
- Canary with no automated success criteria → human judgment under stress.
- No rollback drill → "it worked last time" until it doesn't.

## 6. Revision checklist
- Blue/green vs canary in one line each: ______
- Why expand-contract migrations: ______
- Feature flag's release-vs-deploy decoupling: ______
- One thing every team should drill: ______
