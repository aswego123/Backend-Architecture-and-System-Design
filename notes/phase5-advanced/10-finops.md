# Cost-aware architecture (FinOps)

> Phase 5 · Tags: `cost` `cloud`

## 1. The concept
**FinOps** = engineering practices that treat cloud spend as a first-class concern: visibility, allocation, optimization.

Three loops (FinOps Foundation):
- **Inform**: who spent what, on what.
- **Optimize**: rightsizing, reserved/spot, schedule off-hours, cheaper services.
- **Operate**: continuously iterate; bake cost into design decisions.

## 2. The rule / the why
Cloud bills don't go down by accident. Architectural decisions made without cost awareness compound into surprise invoices.

## 3. Java-specific behavior
- Right-size JVM heap to actual usage (not max possible).
- ARM-based instances (Graviton) often 20–40% cheaper for Java workloads with comparable performance.
- Native image (GraalVM) reduces memory footprint → smaller instances.

## 4. System design angle
- **The big cost levers** (most apps): EC2/compute right-sizing, S3 storage class tiering, data transfer (egress), idle managed services, snapshots/backups never deleted.
- Tagging strategy is the foundation — without tags you can't allocate cost.
- Reserved/savings plans for steady state; spot for fault-tolerant workloads.
- Architecture choices that bite: chatty cross-region calls (egress $$$), oversized always-on stacks for low-traffic services, DynamoDB on-demand misuse.

## 5. Common mistakes / traps
- "We'll optimize later" — costs compound.
- Logging everything at DEBUG → storage + ingestion cost dwarfs compute.
- Idle dev/staging running 24/7.
- Cross-AZ traffic — often cheap to ignore until you find a chatty service paying for it.
- Multi-region active-active when active-passive would do.

## 6. Revision checklist
- Three FinOps loops: ______
- Top 3 cost levers: ______
- Why tagging matters: ______
- One ARM/Graviton benefit for Java: ______
