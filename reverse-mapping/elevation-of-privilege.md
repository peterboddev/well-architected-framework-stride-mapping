# E — Elevation of Privilege: STRIDE → WAFR Reverse Mapping

## "How can someone gain access they shouldn't have?"

### Threat Description
An attacker escalates from lower privileges to higher privileges, gaining unauthorized access to systems, data, or operations beyond their intended permissions.

### Attack Vectors
- IAM policy misconfiguration (wildcards, self-escalation paths)
- Application authorization bypass (IDOR, BOLA, broken access control)
- Container/VM escape (kernel exploit, misconfigured capabilities)
- Code injection leading to RCE (SQLi, RCE, SSRF → IAM role)
- Credential theft + lateral movement (stolen role → assume other roles)
- Orphaned permissions (decommissioned user/role still has access)
- Pipeline compromise (deploy arbitrary code to production)
- Social engineering → admin access
- Confused deputy (service acts on behalf of attacker with its own elevated permissions)

### WAFR Questions to Answer

| Priority | Question | Why It Matters for Elevation of Privilege |
|----------|----------|-----------------------------|
| ●● Critical | SEC 2 — Identity management | Identity compromise is #1 path to escalation |
| ●● Critical | SEC 3 — Permissions | Least privilege, permission boundaries, access controls |
| ●● Critical | SEC 6 — Compute protection | Prevents local escalation, container breakout |
| ●● Critical | SEC 11 — Application security | Catches authorization bypass, injection → RCE |
| ●● Critical | REL 3 — Service architecture | Service boundaries ARE authorization boundaries |
| ●● Critical | COST 2 — Govern usage | SCPs and permission boundaries = hard privilege ceilings |
| ●● Critical | COST 3 — Monitor cost | Unusual spending = top signal of credential abuse |
| ● High | SEC 1 — Secure operations | Separation of duties for operators |
| ● High | SEC 4 — Detection | Detects unusual privilege usage patterns |
| ● High | SEC 5 — Network protection | Segmentation prevents lateral movement |
| ● High | SEC 10 — Incident response | Revokes escalated access during incidents |
| ● High | REL 1 — Service quotas | Quotas limit blast radius of compromised credentials |
| ● High | REL 2 — Network topology | VPC segmentation limits lateral movement paths |
| ● High | REL 8 — Implement change | Change authorization gates prevent unauthorized production access |
| ● High | REL 10 — Fault isolation | Compromised component has bounded permissions |
| ● High | REL 13 — Disaster recovery | Break-glass procedures must be strictly controlled |
| ● High | OPS 2 — Org structure | Separation of duties prevents insider abuse |
| ● High | OPS 4 — Observability | Traces reveal unauthorized access paths |
| ● High | OPS 5 — CI/CD pipeline | Pipeline permissions prevent unauthorized deployment |
| ● High | OPS 6 — Deployment risks | Approval gates prevent unauthorized production changes |
| ● High | OPS 7 — Readiness | Confirms RBAC tested pre-launch |
| ● High | OPS 8 — Workload observability | Real-time detection of privilege abuse |
| ● High | COST 4 — Decommission resources | Orphaned roles retain excessive permissions |
| ● High | SUS 6 — Keep updated | Patches close known escalation CVEs |

### Defense Strategy
```
Priority 1: Enforce least privilege with hard ceilings
  → IAM Permission Boundaries + SCPs
  → No Action: "*", no Resource: "*" on write operations
  → IAM Access Analyzer to detect over-permissive policies
  → SEC 3 + COST 2

Priority 2: Prevent application-level escalation
  → OWASP Top 10 testing (BOLA, broken access control)
  → WAF rules for injection prevention
  → API authorization at every endpoint (not just at gateway)
  → SEC 11 + SEC 5

Priority 3: Harden compute against local escalation
  → No root in containers, read-only filesystem, drop capabilities
  → Nitro hypervisor isolation, no SSH/RDP access
  → Patch OS and runtime against kernel escalation CVEs
  → SEC 6 + SUS 6

Priority 4: Segment to limit blast radius
  → Per-service IAM roles (not shared), per-pod IRSA
  → VPC segmentation, Security Groups as micro-perimeters
  → Cell-based architecture for tenant isolation
  → REL 3 + REL 10 + SEC 5

Priority 5: Detect privilege abuse
  → GuardDuty findings for unusual API calls
  → CloudTrail + Athena queries for AssumeRole, CreateRole, AttachPolicy
  → Cost anomaly detection for unauthorized resource provisioning
  → SEC 4 + OPS 4 + COST 3

Priority 6: Control change paths
  → Pipeline approval gates, no self-approve
  → Separation of duties (dev ≠ deployer ≠ approver)
  → REL 8 + OPS 5 + OPS 6 + OPS 2

Priority 7: Clean up privilege creep
  → Regular access reviews, IAM Access Analyzer
  → Decommission orphaned roles/users
  → COST 4 + SEC 3
```
