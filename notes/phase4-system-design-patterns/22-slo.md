# SLI / SLO / SLA & error budgets

> Phase 4 · Tags: `reliability` `sre`

## 1. The concept
- **SLI** (Service Level Indicator): a metric of user-perceived quality (e.g., `99% of /checkout requests under 300ms`).
- **SLO** (Service Level Objective): the target for that SLI (e.g., 99.9% over 30 days).
- **SLA** (Service Level Agreement): the *external contract*, including penalties. Usually weaker than SLO.
- **Error budget**: `100% – SLO` — how much unreliability you're allowed. Used as a release-velocity throttle.

## 2. The rule / the why
"99.9% available" is meaningless without a definition. SLIs make reliability measurable. Error budgets convert reliability into a quantitative engineering trade-off: if you have budget, ship faster; if you don't, stop and stabilize.

## 3. Java-specific behavior
- Pull SLIs from Micrometer metrics (success ratio, latency histogram p99).
- Wire SLO burn-rate alerts in Prometheus / Grafana / Datadog.
- Common SLIs:
  - **Availability**: `successes / total` over a sliding window.
  - **Latency**: percentile (p95, p99) under threshold.
  - **Quality**: e.g., search relevance, freshness lag.

## 4. System design angle
- SLOs are picked from user expectations, not engineering aspiration. "99.999%" is often unnecessary and unaffordable.
- Burn-rate alerts > raw threshold alerts. "We've burned 5% of budget in 1 hour" tells you to act fast.
- Multi-window burn-rate alerts (Google SRE): short window (1h) + long window (6h) reduces both false positives and false negatives.

## 5. Common mistakes / traps
- Promising 99.99% to customers because "more 9s is better".
- SLI based on synthetic probes — doesn't reflect real user experience.
- Ignoring the budget: shipping fast even after budget is exhausted.
- Single SLO for a complex service — split by critical user journey.
- Excluding "planned maintenance" from your SLO → users still see the outage.

## 6. Revision checklist
- SLI vs SLO vs SLA: ______
- Error budget formula: ______
- Why burn-rate alerts beat raw alerts: ______
- One SLI for a payment API: ______
