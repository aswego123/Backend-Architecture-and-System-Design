# Chaos engineering

> Phase 4 · Tags: `reliability`

## 1. The concept
Chaos engineering = deliberately injecting failures into a running system to find weaknesses before real outages do.

Levels of failure to inject:
- Latency / packet loss (network).
- Kill a pod / VM.
- CPU / memory pressure.
- DNS failure.
- Dependency timeout.
- Region outage (advanced).

Tools: Chaos Monkey (Netflix), Chaos Mesh, LitmusChaos, Gremlin, AWS FIS.

## 2. The rule / the why
You think your system handles failure because you wrote retries and circuit breakers. Chaos engineering proves whether it actually does — under realistic, partial-failure conditions you can't see in unit tests.

## 3. Java-specific behavior
- Resilience4j + Toxiproxy: inject latency/disconnects in integration tests.
- Spring Boot + chaos starter (`codecentric/chaos-monkey-spring-boot`) — runtime failure injection inside the app.

## 4. System design angle
- **Game days**: scheduled exercises where the team simulates an incident and practices response.
- Start in staging, then in prod (in a blast-radius-limited way).
- Each experiment has a hypothesis: "killing instance X should result in zero user-visible errors". Falsify or confirm.
- Pair with observability — chaos without metrics is just sabotage.

## 5. Common mistakes / traps
- Running chaos in prod without a kill switch.
- No hypothesis → no learning.
- Once-a-year exercise → finds yesterday's bugs, not today's.
- Chaos that bypasses safeguards you actually rely on (don't kill the only DB).
- Skipping the postmortem: every chaos test → "what would we fix?".

## 6. Revision checklist
- Why deliberately break things: ______
- One failure to inject: ______
- The hypothesis-driven angle: ______
- One safety rail before doing it in prod: ______
