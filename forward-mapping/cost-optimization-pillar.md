# Cost Optimization Pillar → STRIDE Mapping

### COST 1: How do you implement cloud financial management?
**Area:** Cloud Financial Management

| STRIDE | Relevance | Rationale |
|--------|-----------|-----------|
| S | ○ Low | Not directly related |
| T | ○ Low | Not directly related |
| R | ○ Medium | Financial governance creates audit trails for spending decisions |
| I | ○ Low | Not directly related |
| D | ○ Medium | Budget alerts can detect resource abuse that threatens availability |
| E | ○ Medium | Financial controls (SCPs on resource creation) limit unauthorized provisioning |

---

### COST 2: How do you govern usage?
**Area:** Expenditure and Usage Awareness

| STRIDE | Relevance | Rationale |
|--------|-----------|-----------|
| **S** | ● High | Usage governance (SCPs, IAM boundaries) validates WHO is creating resources |
| **T** | ● High | Preventive controls (SCPs, tag policies) prevent unauthorized infrastructure changes |
| R | ● High | Usage governance creates audit trail for all resource creation/modification |
| I | ○ Medium | Governance controls can restrict data-related service usage (e.g., block public S3) |
| D | ● High | Budget controls and quota management prevent resource starvation and abuse |
| **E** | ●● Critical | SCPs, permission boundaries, and governance guardrails ARE privilege escalation prevention |

---

### COST 3: How do you monitor usage and cost?
**Area:** Expenditure and Usage Awareness

| STRIDE | Relevance | Rationale |
|--------|-----------|-----------|
| **S** | ● High | Cost spikes from unauthorized accounts/regions indicate credential compromise (spoofed identity) |
| T | ○ Medium | Unexpected infrastructure changes show up as cost anomalies |
| R | ● High | Cost monitoring provides independent evidence trail of resource usage per identity |
| I | ○ Medium | Data transfer cost spikes can indicate exfiltration |
| **D** | ● High | Crypto-mining and resource abuse drives costs AND exhausts capacity (DoS via resource consumption) |
| **E** | ●● Critical | Unusual spending = #1 signal of compromised credentials provisioning unauthorized resources |

---

### COST 4: How do you decommission resources?
**Area:** Expenditure and Usage Awareness

| STRIDE | Relevance | Rationale |
|--------|-----------|-----------|
| **S** | ● High | Orphaned resources with stale credentials become impersonation vectors (forgotten service accounts) |
| T | ○ Medium | Abandoned resources may run unpatched software, vulnerable to tampering |
| R | ○ Medium | Decommissioning without audit trail loses accountability evidence |
| **I** | ●● Critical | Decommissioned resources containing unwiped data are a major breach risk (EBS snapshots, S3 buckets, RDS backups) |
| D | ○ Medium | Orphaned resources consume capacity, can be weaponized against your own infrastructure |
| **E** | ● High | Orphaned IAM roles, security groups, and endpoints retain excessive permissions and become attack paths |

---

### COST 5: How do you evaluate cost when you select services?
**Area:** Cost-effective Resources

| STRIDE | Relevance | Rationale |
|--------|-----------|-----------|
| S | ○ Medium | Service selection determines built-in auth capabilities (Cognito vs DIY, API Gateway vs custom) |
| T | ○ Medium | Managed services include built-in integrity (automated patching, checksums) |
| R | ○ Medium | Service selection determines available audit features (CloudTrail integration depth) |
| I | ○ Medium | Service selection determines encryption options and data handling (SSE, CMK, client-side) |
| D | ○ Medium | Managed services include built-in HA; serverless eliminates server-level DoS |
| E | ○ Medium | Services vary in IAM integration depth and granularity of access controls |

---

### COST 6: How do you meet cost targets when you select resource type, size and number?
**Area:** Cost-effective Resources

| STRIDE | Relevance | Rationale |
|--------|-----------|-----------|
| S | ○ Low | Not directly related |
| T | ○ Medium | Under-resourced systems may skip integrity checks under load (validation bypassed) |
| R | ○ Low | Not directly related |
| I | ○ Low | Not directly related |
| **D** | ● High | Under-provisioning to save cost creates direct availability risk under load spikes |
| E | ○ Medium | Resource-constrained systems may fail-open on auth (rate limiter, WAF disabled under pressure) |

---

### COST 7: How do you use pricing models to reduce cost?
**Area:** Cost-effective Resources

| STRIDE | Relevance | Rationale |
|--------|-----------|-----------|
| S | ○ Low | Not directly related |
| T | ○ Low | Not directly related |
| R | ○ Low | Not directly related |
| I | ○ Low | Not directly related |
| D | ○ Low | Not directly related |
| E | ○ Low | Not directly related |

*No significant STRIDE relevance.*

---

### COST 8: How do you plan for data transfer charges?
**Area:** Cost-effective Resources

| STRIDE | Relevance | Rationale |
|--------|-----------|-----------|
| S | ○ Low | Not directly related |
| T | ○ Medium | Data transfer path selection determines which links need integrity protection |
| R | ○ Low | Not directly related |
| **I** | ● High | Data transfer paths define exposure surface — public internet vs PrivateLink vs VPC peering |
| D | ○ Medium | Consolidating transfer paths creates single points of failure |
| E | ○ Low | Not directly related |

---

### COST 9: How do you manage demand, and supply resources?
**Area:** Manage Demand and Supply

| STRIDE | Relevance | Rationale |
|--------|-----------|-----------|
| S | ○ Medium | Throttling per-identity prevents impersonation-based resource abuse |
| T | ○ Medium | Queue-based processing enables input validation before processing |
| R | ○ Medium | Queued requests with IDs provide processing audit trail |
| I | ○ Low | Not directly related |
| **D** | ●● Critical | Throttling, queuing, and buffer are direct DoS mitigations — absorb spikes without crash |
| E | ○ Medium | Per-caller throttling prevents single actor from monopolizing resources |

---

### COST 10: How do you evaluate new services?
**Area:** Optimize Over Time

| STRIDE | Relevance | Rationale |
|--------|-----------|-----------|
| S | ○ Low | Not directly related |
| T | ○ Low | Not directly related |
| R | ○ Low | Not directly related |
| I | ○ Low | Not directly related |
| D | ○ Low | Not directly related |
| E | ○ Low | Not directly related |

*No significant STRIDE relevance.*

---

### COST 11: How do you evaluate the cost of effort?
**Area:** Optimize Over Time

| STRIDE | Relevance | Rationale |
|--------|-----------|-----------|
| S | ○ Low | Not directly related |
| T | ○ Low | Not directly related |
| R | ○ Low | Not directly related |
| I | ○ Low | Not directly related |
| D | ○ Low | Not directly related |
| E | ○ Low | Not directly related |

*No significant STRIDE relevance.*
