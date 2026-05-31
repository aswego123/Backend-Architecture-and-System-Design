# One cloud at intro level

> Phase 2 · Tags: `devops` `cloud`

## 1. The concept
Pick **one** cloud (AWS or GCP) and learn the core building blocks. They map across providers — concepts transfer, names don't.

Core blocks every backend uses:

| Need | AWS | GCP |
|---|---|---|
| Compute (VMs) | EC2 | Compute Engine |
| Containers (managed) | ECS / EKS | Cloud Run / GKE |
| Serverless functions | Lambda | Cloud Functions |
| Object storage | S3 | Cloud Storage |
| Managed RDB | RDS | Cloud SQL |
| Managed cache | ElastiCache | Memorystore |
| Managed queue/stream | SQS / Kinesis / MSK | Pub/Sub / Managed Kafka |
| DNS | Route 53 | Cloud DNS |
| Load balancer | ALB / NLB | Cloud Load Balancing |
| Secrets | Secrets Manager | Secret Manager |
| IAM | IAM | IAM |
| Observability | CloudWatch | Cloud Monitoring |

## 2. The rule / the why
The cloud removes the "owning hardware" problem but adds the "configuring services" problem. Skill = knowing which managed service to reach for instead of building it yourself.

## 3. Java-specific behavior
- AWS SDK v2 for Java (`software.amazon.awssdk:*`). Use the async clients for non-blocking I/O.
- Spring Cloud AWS for idiomatic integration (`@SqsListener`, etc.).
- Don't use credential files in containers — use IAM roles for service accounts (EKS IRSA, ECS task roles, GKE Workload Identity).

## 4. System design angle
- **Stateless services + managed data services** is the easy mode.
- Multi-AZ from day one is cheap insurance; multi-region is much harder and rarely needed early.
- Cost: pay attention to data transfer (egress), idle resources, and "always-on" managed services.
- IaC (Terraform, Pulumi, AWS CDK) — never click in the console for prod.

## 5. Common mistakes / traps
- Long-lived static credentials in env vars or git.
- Public S3 buckets with sensitive data.
- One giant VPC + no network segmentation.
- No tagging strategy → can't track cost.
- Locking into a high-level managed service (e.g., DynamoDB) without understanding its cost/access model.

## 6. Revision checklist
- Map 3 "I need…" needs to a service in your chosen cloud: ______
- Why IaC over console clicks: ______
- The "static credentials" anti-pattern fix: ______
- One cost trap: ______
