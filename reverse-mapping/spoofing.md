# S — Spoofing: STRIDE → WAFR Reverse Mapping

## "Who or what can pretend to be something it's not?"

### Threat Description
An attacker impersonates a legitimate user, service, system, or data source to gain unauthorized access or mislead other components.

### Attack Vectors
- Stolen/leaked credentials (API keys, access keys, passwords)
- Session hijacking and token theft
- DNS spoofing / cache poisoning
- Service impersonation (rogue endpoint pretending to be legitimate)
- Supply chain spoofing (malicious package with legitimate name)
- Social engineering / phishing (human identity spoofing)
- Replay attacks (reuse of valid auth tokens)

### WAFR Questions to Answer

| Priority | Question | Why It Matters for Spoofing |
|----------|----------|-----------------------------|
| ●● Critical | SEC 2 — Manage identities | Directly prevents impersonation through strong identity (MFA, federation, short-lived creds) |
| ● High | SEC 9 — Data in transit | TLS/mTLS prevents MITM impersonation of endpoints |
| ● High | SEC 1 — Secure operations | Operator identity verification and separation of duties |
| ● High | SEC 3 — Manage permissions | Validates that authenticated identity actually has claimed privileges |
| ● High | SEC 4 — Detection | Detects successful and attempted impersonation (impossible travel, credential stuffing) |
| ● High | SEC 5 — Network protection | Prevents IP/DNS spoofing at network layer |
| ● High | SEC 11 — Application security | Validates auth implementation in code (no auth bypass bugs) |
| ● High | REL 2 — Network topology | Trust zones define what can authenticate to what |
| ● High | REL 3 — Service architecture | Service boundaries require explicit service-to-service auth |
| ● High | OPS 4 — Observability | Anomaly detection catches impersonation in real-time |
| ● High | OPS 5 — CI/CD pipeline | Signed commits and verified build agents prevent supply chain spoofing |
| ● High | OPS 7 — Readiness | Confirms auth mechanisms are tested pre-launch |
| ● High | OPS 8 — Workload observability | Real-time detection of active impersonation |
| ● High | PERF 4 — Networking/CDN | Edge auth (signed URLs, origin access) validates at perimeter |
| ● High | COST 2 — Govern usage | SCPs validate WHO creates resources |
| ● High | COST 3 — Monitor cost | Cost spikes from unknown regions = credential compromise |
| ● High | COST 4 — Decommission | Orphaned service accounts become spoofing vectors |

### Defense Strategy
```
Priority 1: Eliminate long-lived credentials
  → AWS IAM Identity Center, IAM Roles, STS temporary credentials
  → SEC 2 + SEC 3

Priority 2: Authenticate everything
  → mTLS between services (Private CA, App Mesh)
  → Signed requests (SigV4) for all API calls
  → REL 3 + SEC 9

Priority 3: Detect impersonation
  → GuardDuty credential compromise findings
  → CloudTrail anomaly detection
  → SEC 4 + OPS 4 + COST 3

Priority 4: Prevent at the perimeter
  → CloudFront signed URLs, API Gateway authorizers
  → Route 53 DNSSEC
  → SEC 5 + PERF 4
```
