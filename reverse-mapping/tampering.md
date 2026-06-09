# T — Tampering: STRIDE → WAFR Reverse Mapping

## "Who or what can modify data, code, or configuration without authorization?"

### Threat Description
An attacker modifies data at rest, data in transit, application code, infrastructure configuration, or deployment artifacts to corrupt systems or inject malicious logic.

### Attack Vectors
- SQL injection / NoSQL injection / command injection
- Man-in-the-middle modification of network traffic
- Unauthorized configuration changes (drift, insider)
- Supply chain attacks (compromised dependencies, poisoned images)
- Ransomware / wiper malware (destructive tampering)
- Replay/modification of API requests
- Build pipeline poisoning (malicious code injected during CI/CD)
- Database/storage corruption (direct modification via stolen creds)

### WAFR Questions to Answer

| Priority | Question | Why It Matters for Tampering |
|----------|----------|-----------------------------|
| ●● Critical | SEC 6 — Compute protection | Immutable infrastructure, patching, runtime integrity |
| ●● Critical | SEC 9 — Data in transit | Encryption + integrity checks prevent in-flight modification |
| ●● Critical | SEC 11 — Application security | Input validation, injection prevention, code integrity |
| ●● Critical | REL 8 — Implement change | Controlled, authorized, reversible changes only |
| ●● Critical | OPS 5 — CI/CD pipeline | Signed artifacts, verified provenance, pipeline integrity |
| ●● Critical | OPS 6 — Deployment risks | Rollback from corrupted deployments |
| ● High | SEC 8 — Data at rest | Integrity controls on stored data (checksums, versioning) |
| ● High | SEC 1 — Secure operations | Configuration integrity and runtime controls |
| ● High | SEC 3 — Permissions | Write permissions control who can modify |
| ● High | SEC 4 — Detection | Integrity monitoring detects unauthorized changes |
| ● High | SEC 5 — Network protection | Network encryption prevents packet modification |
| ● High | SEC 10 — Incident response | Containment and restoration from tampering |
| ● High | REL 4 — Prevent failures | Idempotency prevents replay attacks from corrupting state |
| ● High | REL 5 — Withstand failures | Graceful degradation maintains consistency |
| ● High | REL 6 — Monitor resources | Detects config drift and unauthorized state changes |
| ● High | REL 9 — Back up data | Recovery from data corruption/ransomware |
| ● High | REL 10 — Fault isolation | Contains blast radius of corrupted components |
| ● High | REL 11 — Component failures | Redundancy enables recovery from corruption |
| ● High | REL 12 — Test reliability | Chaos testing validates integrity under failure |
| ● High | REL 13 — Disaster recovery | Recovery from large-scale tampering (ransomware) |
| ● High | OPS 2 — Org structure | Separation of duties prevents unauthorized modifications |
| ● High | OPS 7 — Readiness | Validates integrity controls before go-live |
| ● High | OPS 10 — Operations events | Runbooks for tampering response |
| ● High | OPS 11 — Evolve operations | Post-incident integrity improvements |
| ● High | PERF 2 — Compute selection | Hardware security (Nitro, TPM) for tamper resistance |
| ● High | PERF 3 — Data management | ACID, WAL, checksums in data store selection |
| ● High | PERF 4 — Networking/CDN | Content integrity validation at edge |
| ● High | COST 2 — Govern usage | SCPs block unauthorized infrastructure changes |
| ● High | SUS 6 — Keep updated | Patches close known integrity vulnerabilities |

### Defense Strategy
```
Priority 1: Make infrastructure immutable
  → CDK/CloudFormation (no manual changes), ECR image signing, AMI baking
  → SEC 6 + OPS 5

Priority 2: Protect data integrity
  → KMS encryption + HMAC, S3 Object Lock, DynamoDB conditional writes
  → SEC 8 + SEC 9

Priority 3: Control all changes
  → CodePipeline approvals, CloudFormation change sets, Config rules
  → REL 8 + OPS 6

Priority 4: Detect and recover from tampering
  → AWS Config drift detection, CloudTrail integrity validation
  → AWS Backup with cross-account immutable vaults
  → SEC 4 + REL 9 + REL 13

Priority 5: Validate inputs everywhere
  → WAF managed rules (SQLi, XSS), API Gateway request validation
  → SEC 11 + SEC 5
```
