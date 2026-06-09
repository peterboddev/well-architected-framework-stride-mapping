# D — Denial of Service: STRIDE → WAFR Reverse Mapping

## "How can this system be made unavailable?"

### Threat Description
An attacker (or misconfiguration, or cascading failure) renders the system unavailable to legitimate users, either through resource exhaustion, component destruction, or cascading failure.

### Attack Vectors
- Volumetric DDoS (network/bandwidth flooding)
- Application-layer DDoS (expensive queries, login floods)
- Resource exhaustion (CPU, memory, disk, connections, quotas)
- Cascading failure (one component failure spreads)
- Self-inflicted DoS (bad deployment, config error, runaway process)
- Destructive attacks (ransomware, wiper, data deletion)
- Dependency failure (downstream service unavailable)
- Quota exhaustion (hitting AWS service limits)
- Cost-based DoS (attacker drives spend until budget cuts service)

### WAFR Questions to Answer

| Priority | Question | Why It Matters for Denial of Service |
|----------|----------|-----------------------------|
| ●● Critical | SEC 5 — Network protection | DDoS protection, WAF, rate limiting at perimeter |
| ●● Critical | REL 1 — Service quotas | Prevents resource exhaustion by enforcing limits |
| ●● Critical | REL 2 — Network topology | Redundant topology eliminates single points of failure |
| ●● Critical | REL 3 — Service architecture | Fault isolation via service boundaries |
| ●● Critical | REL 4 — Prevent failures | Circuit breakers, timeouts prevent cascading failure |
| ●● Critical | REL 5 — Withstand failures | Bulkheads, retry with backoff, queue-based leveling |
| ●● Critical | REL 6 — Monitor resources | Detect capacity exhaustion before outage |
| ●● Critical | REL 7 — Adapt to demand | Auto-scaling absorbs traffic spikes |
| ●● Critical | REL 9 — Back up data | Recovery from destructive attacks |
| ●● Critical | REL 10 — Fault isolation | Blast radius containment (cell architecture) |
| ●● Critical | REL 11 — Component failures | Redundancy maintains availability during failures |
| ●● Critical | REL 12 — Test reliability | Chaos engineering validates resilience |
| ●● Critical | REL 13 — Disaster recovery | Recovery from catastrophic events |
| ●● Critical | OPS 10 — Operations events | Event management handles availability incidents |
| ●● Critical | PERF 4 — Networking/CDN | CDN absorbs volumetric attacks at edge |
| ●● Critical | COST 9 — Manage demand | Throttling, queuing are direct DoS mitigations |
| ● High | SEC 4 — Detection | Detects anomalous traffic patterns |
| ● High | SEC 6 — Compute protection | Resource protection ensures compute availability |
| ● High | SEC 10 — Incident response | Recovery procedures restore availability |
| ● High | REL 8 — Implement change | Controlled rollouts prevent self-inflicted outage |
| ● High | OPS 4 — Observability | Early degradation detection |
| ● High | OPS 6 — Deployment risks | Canary deploys prevent deployment-caused outages |
| ● High | OPS 7 — Readiness | Failover and availability mechanisms tested |
| ● High | OPS 8 — Workload observability | Alert-driven response to availability issues |
| ● High | OPS 9 — Operations health | Early warning for operational issues |
| ● High | OPS 11 — Evolve operations | Post-incident availability improvements |
| ● High | PERF 1 — Architecture selection | Right-sized architecture for workload demands |
| ● High | PERF 2 — Compute selection | Prevents performance degradation under load |
| ● High | PERF 3 — Data management | Prevents I/O bottleneck-driven unavailability |
| ● High | PERF 5 — Performance process | Load testing reveals breaking points |
| ● High | COST 2 — Govern usage | Budget/quota controls prevent resource starvation |
| ● High | COST 3 — Monitor cost | Cost spikes indicate resource abuse |
| ● High | COST 6 — Resource sizing | Under-provisioning creates availability risk |
| ● High | SUS 2 — Align to demand | Right-sizing prevents capacity exhaustion |
| ● High | SUS 3 — Architecture patterns | Efficient architectures are more resilient |
| ● High | SUS 6 — Keep updated | Patches close known DoS exploits |

### Defense Strategy
```
Priority 1: Absorb at the edge
  → Shield Advanced + WAF + CloudFront
  → Rate limiting, geo-blocking, bot management
  → SEC 5 + PERF 4

Priority 2: Scale with demand
  → Auto Scaling groups, Lambda concurrency, ECS task scaling
  → API Gateway throttling, SQS load leveling
  → REL 7 + COST 9

Priority 3: Isolate failures
  → Cell-based architecture, shuffle sharding
  → Circuit breakers, bulkheads, timeouts
  → REL 3 + REL 4 + REL 5 + REL 10

Priority 4: Eliminate single points of failure
  → Multi-AZ everything, Route 53 health checks + failover
  → Cross-region DR for catastrophic scenarios
  → REL 2 + REL 11 + REL 13

Priority 5: Monitor and manage quotas
  → Service Quotas dashboard, proactive increases
  → CloudWatch alarms on capacity approach
  → REL 1 + REL 6

Priority 6: Prevent self-inflicted DoS
  → Canary/rolling deployments, automated rollback
  → Chaos engineering to validate resilience
  → OPS 6 + REL 12

Priority 7: Recover from destructive attacks
  → Cross-account immutable backups, tested DR runbooks
  → REL 9 + REL 13
```
