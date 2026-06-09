# R — Repudiation: STRIDE → WAFR Reverse Mapping

## "Can someone deny having performed an action?"

### Threat Description
An actor (user, service, or system) performs an action and later denies it, with insufficient evidence to prove otherwise. This undermines accountability, forensics, compliance, and legal proceedings.

### Attack Vectors
- Shared credentials (can't attribute action to individual)
- Insufficient logging (no record of action)
- Log tampering or deletion (destroying evidence)
- Time manipulation (falsifying when action occurred)
- Anonymous/unauthenticated actions (no identity to attribute)
- Lack of log integrity verification (undetectable modification)
- Gaps in distributed tracing (actions lost between services)

### WAFR Questions to Answer

| Priority | Question | Why It Matters for Repudiation |
|----------|----------|-----------------------------|
| ●● Critical | SEC 4 — Detection | Logging and audit trails make ALL actions provable |
| ●● Critical | OPS 4 — Observability | Logs, traces, and metrics provide irrefutable evidence chain |
| ●● Critical | OPS 8 — Workload observability | Continuous evidence chain across production operations |
| ● High | SEC 1 — Secure operations | Operational audit trails for operator actions |
| ● High | SEC 2 — Identity management | Identity binding = every action has an attributable actor |
| ● High | SEC 10 — Incident response | Forensics, evidence preservation, chain of custody |
| ● High | REL 6 — Monitor resources | Continuous audit evidence of system behavior |
| ● High | REL 8 — Implement change | Change logs attribute every modification to an actor |
| ● High | REL 13 — Disaster recovery | Forensic evidence preservation during DR |
| ● High | OPS 2 — Org structure | Defined ownership = clear accountability |
| ● High | OPS 3 — Culture | Blame-free culture encourages reporting (not hiding) |
| ● High | OPS 5 — CI/CD pipeline | Pipeline provenance — who merged, who approved, who deployed |
| ● High | OPS 6 — Deployment risks | Deployment audit trails |
| ● High | OPS 7 — Readiness | Confirms audit/logging is operational pre-launch |
| ● High | OPS 10 — Operations events | Event documentation with timeline and parties |
| ● High | PERF 3 — Data management | Database audit features (DynamoDB Streams, RDS audit logs) |
| ● High | COST 2 — Govern usage | Governance audit trail for resource creation |
| ● High | COST 3 — Monitor cost | Independent spending evidence per identity |

### Defense Strategy
```
Priority 1: Log everything, immutably
  → CloudTrail (all regions, org trail), CloudTrail integrity validation
  → S3 Object Lock for log archives
  → SEC 4 + SEC 8

Priority 2: Attribute every action to a unique identity
  → No shared credentials, no generic accounts
  → IAM Identity Center with individual users
  → SEC 2

Priority 3: Implement distributed tracing
  → X-Ray / OpenTelemetry for request attribution across services
  → Correlation IDs from edge to database
  → OPS 4 + OPS 8

Priority 4: Protect log integrity
  → CloudWatch Logs immutability, CloudTrail Lake
  → Cross-account log shipping (attacker can't delete)
  → SEC 4 + SEC 8

Priority 5: Ensure independent audit signals
  → Cost monitoring provides independent evidence (COST 3)
  → VPC Flow Logs provide network-level evidence
  → DynamoDB Streams / RDS audit logs for data-level evidence
  → PERF 3 + COST 3
```
