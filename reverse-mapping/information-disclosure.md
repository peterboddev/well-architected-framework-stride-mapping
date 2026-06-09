# I — Information Disclosure: STRIDE → WAFR Reverse Mapping

## "What sensitive data can be exposed to unauthorized parties?"

### Threat Description
Sensitive data (PII, credentials, business data, system internals) is exposed to unauthorized parties through misconfiguration, insufficient controls, or active exploitation.

### Attack Vectors
- Publicly accessible storage (open S3 buckets, public RDS snapshots)
- Unencrypted data at rest or in transit
- Overly permissive read access (IAM, database, file permissions)
- Error messages leaking internal state (stack traces, connection strings)
- Side-channel attacks (memory dumps, CPU cache timing)
- Data exfiltration via DNS, HTTPS, or authorized channels
- Orphaned resources with retained sensitive data
- Cross-tenant data leakage (shared infrastructure)
- Backup/snapshot exposure (unencrypted, wrong account access)

### WAFR Questions to Answer

| Priority | Question | Why It Matters for Information Disclosure |
|----------|----------|-----------------------------|
| ●● Critical | SEC 5 — Network protection | Segmentation prevents eavesdropping and data flow exposure |
| ●● Critical | SEC 7 — Data classification | Enables right-sized controls based on sensitivity |
| ●● Critical | SEC 8 — Data at rest | Encryption prevents unauthorized access to stored data |
| ●● Critical | SEC 9 — Data in transit | TLS prevents eavesdropping on data in motion |
| ●● Critical | COST 4 — Decommission resources | Unwiped decommissioned resources are major breach risk |
| ●● Critical | SUS 4 — Data management policies | Data that doesn't exist can't be exfiltrated |
| ● High | SEC 3 — Permissions | Read permissions control who can access data |
| ● High | SEC 4 — Detection | Detects exfiltration patterns |
| ● High | SEC 6 — Compute protection | Prevents memory dumps, side-channel attacks |
| ● High | SEC 10 — Incident response | Limits ongoing exposure during breach |
| ● High | SEC 11 — Application security | Catches data leakage vulnerabilities in code |
| ● High | REL 2 — Network topology | Topology defines data flow exposure (public vs private) |
| ● High | REL 3 — Service architecture | Service boundaries are data boundaries |
| ● High | REL 9 — Back up data | Backups contain sensitive data and need same encryption |
| ● High | REL 10 — Fault isolation | Breach in one cell doesn't expose adjacent cells |
| ● High | REL 13 — Disaster recovery | DR sites must maintain same confidentiality controls |
| ● High | OPS 5 — CI/CD pipeline | Secrets in build artifacts and environment variables |
| ● High | OPS 7 — Readiness | Confirms encryption and data protection pre-launch |
| ● High | PERF 2 — Compute selection | Nitro Enclaves for sensitive data processing |
| ● High | PERF 3 — Data management | Storage encryption options per data store |
| ● High | PERF 4 — Networking/CDN | TLS termination points and decryption locations |
| ● High | COST 8 — Data transfer | Transfer paths define exposure surface |
| ● High | SUS 1 — Region selection | Data residency and sovereignty |

### Defense Strategy
```
Priority 1: Encrypt everything
  → KMS for data at rest, ACM + TLS 1.2+ for data in transit
  → Enforce via S3 bucket policies and ALB listeners
  → SEC 8 + SEC 9

Priority 2: Minimize data existence
  → Data lifecycle policies (auto-delete after retention period)
  → Don't store what you don't need
  → SUS 4 + COST 4

Priority 3: Segment and isolate
  → VPC, private subnets, VPC endpoints, PrivateLink
  → Per-service data boundaries (each service owns its data)
  → SEC 5 + REL 2 + REL 3 + REL 10

Priority 4: Classify and control access
  → Macie for discovery, Lake Formation for fine-grained access
  → S3 Block Public Access, IAM least-privilege reads
  → SEC 7 + SEC 3

Priority 5: Detect exfiltration
  → GuardDuty (DNS exfil, S3 anomalies), VPC Flow Logs
  → Cost anomalies on data transfer
  → SEC 4 + COST 3

Priority 6: Protect compute-level exposure
  → Nitro Enclaves for sensitive processing
  → No SSH/RDP access (use Session Manager)
  → PERF 2 + SEC 6
```
