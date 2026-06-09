# Actionable Checklist: STRIDE Threat Model Review Using WAR Questions

Use this checklist during threat modeling sessions. For each STRIDE category, answer the listed WAR questions for your specific workload. A "No" or "Partial" answer reveals an unmitigated threat.

## S — Spoofing Checklist

| # | Check | WAR Source | Pass? |
|---|-------|-----------|-------|
| S1 | All human identities use MFA and federated authentication (no shared accounts) | SEC 2 | ☐ |
| S2 | All machine identities use short-lived credentials (IAM roles, IRSA, not long-lived keys) | SEC 2 | ☐ |
| S3 | Service-to-service communication uses authenticated channels (mTLS, IAM auth, service mesh) | REL 3, SEC 9 | ☐ |
| S4 | Network controls prevent IP/DNS spoofing (VPC, private subnets, Route53 DNSSEC) | SEC 5, REL 2 | ☐ |
| S5 | CDN/edge layer validates request authenticity (signed URLs, origin access identity) | PERF 4 | ☐ |
| S6 | CI/CD pipeline verifies identity at each stage (signed commits, verified build agents) | OPS 5 | ☐ |
| S7 | Orphaned service accounts and credentials are inventoried and decommissioned | COST 4 | ☐ |
| S8 | Cost anomalies from unknown regions/accounts trigger identity compromise investigation | COST 3 | ☐ |
| S9 | Observability detects anomalous authentication patterns (impossible travel, unusual hours) | OPS 4, OPS 8 | ☐ |
| S10 | Operational readiness review confirms all auth mechanisms are tested before go-live | OPS 7 | ☐ |

## T — Tampering Checklist

| # | Check | WAR Source | Pass? |
|---|-------|-----------|-------|
| T1 | Compute infrastructure is immutable (AMIs, container images rebuilt not patched in place) | SEC 6 | ☐ |
| T2 | Data at rest has integrity controls (checksums, versioning, write-once storage) | SEC 8 | ☐ |
| T3 | Data in transit uses encryption with integrity verification (TLS 1.2+, HMAC) | SEC 9 | ☐ |
| T4 | Application validates all input and prevents injection (parameterized queries, schema validation) | SEC 11 | ☐ |
| T5 | CI/CD pipeline produces signed artifacts with verifiable provenance (SLSA, Sigstore) | OPS 5 | ☐ |
| T6 | Deployments are controlled, reversible, and audited (canary, rollback, approval gates) | OPS 6, REL 8 | ☐ |
| T7 | Distributed system interactions use idempotency and contract validation | REL 4 | ☐ |
| T8 | Graceful degradation maintains data consistency (no corruption during failover) | REL 5 | ☐ |
| T9 | Backups enable recovery from ransomware/wiper attacks (immutable, cross-account, tested) | REL 9 | ☐ |
| T10 | Fault isolation contains blast radius of corrupted components | REL 10 | ☐ |
| T11 | Chaos testing validates integrity checks work under failure conditions | REL 12 | ☐ |
| T12 | DR plan covers large-scale data tampering scenarios (ransomware recovery tested) | REL 13 | ☐ |
| T13 | Configuration drift is monitored and auto-remediated (Config rules, Terraform drift detection) | REL 6 | ☐ |
| T14 | Database/storage selections provide ACID, WAL, or checksum guarantees as needed | PERF 3 | ☐ |
| T15 | Software dependencies are current and patched against known integrity vulnerabilities | SUS 6 | ☐ |
| T16 | SCPs and preventive controls block unauthorized infrastructure modifications | COST 2 | ☐ |

## R — Repudiation Checklist

| # | Check | WAR Source | Pass? |
|---|-------|-----------|-------|
| R1 | All API calls are logged in CloudTrail (all regions, all accounts, no gaps) | SEC 4 | ☐ |
| R2 | Application-level audit logging captures business-relevant actions with actor identity | OPS 4 | ☐ |
| R3 | Logs are immutable and tamper-evident (S3 Object Lock, CloudWatch Logs Insights) | SEC 8 | ☐ |
| R4 | Identity is bound to every action (no shared credentials, no generic service accounts) | SEC 2 | ☐ |
| R5 | Deployment changes are attributed to specific actors with timestamps | OPS 6, REL 8 | ☐ |
| R6 | Resource creation/modification generates governance audit trail | COST 2 | ☐ |
| R7 | Cost and usage monitoring provides independent evidence of resource consumption per identity | COST 3 | ☐ |
| R8 | Organizational ownership is defined — you can always answer "who owns this?" | OPS 2 | ☐ |
| R9 | Blame-free culture encourages incident reporting (not hiding mistakes) | OPS 3 | ☐ |
| R10 | Distributed tracing (X-Ray, OpenTelemetry) provides end-to-end request attribution | OPS 4, OPS 8 | ☐ |
| R11 | Operational events are documented with timeline and responsible parties | OPS 10 | ☐ |
| R12 | Data store selection provides built-in audit capabilities (DynamoDB Streams, S3 access logs) | PERF 3 | ☐ |
| R13 | Forensic evidence is preserved during incidents (memory dumps, disk snapshots, log exports) | SEC 10, REL 13 | ☐ |

## I — Information Disclosure Checklist

| # | Check | WAR Source | Pass? |
|---|-------|-----------|-------|
| I1 | Data is classified by sensitivity with appropriate controls per level | SEC 7 | ☐ |
| I2 | All data at rest is encrypted (KMS, SSE, client-side as appropriate per classification) | SEC 8 | ☐ |
| I3 | All data in transit uses TLS 1.2+ (no plaintext channels, certificate validation enforced) | SEC 9 | ☐ |
| I4 | Network segmentation prevents unauthorized data flow (private subnets, VPC endpoints, NACLs) | SEC 5, REL 2 | ☐ |
| I5 | Read permissions follow least-privilege (no broad S3 public access, no wildcard IAM reads) | SEC 3 | ☐ |
| I6 | Decommissioned resources have data wiped (EBS, RDS snapshots, S3 objects lifecycle-deleted) | COST 4 | ☐ |
| I7 | Data lifecycle policies minimize retention window (data that doesn't exist can't leak) | SUS 4 | ☐ |
| I8 | Region selection respects data residency and sovereignty requirements | SUS 1 | ☐ |
| I9 | Fault isolation boundaries are also data boundaries (cell-based, per-tenant encryption) | REL 10 | ☐ |
| I10 | DR replication encrypts cross-region data and maintains same access controls | REL 13 | ☐ |
| I11 | Backup data is encrypted and access-controlled equivalent to source | REL 9 | ☐ |
| I12 | Build artifacts and pipelines don't leak secrets (no hardcoded creds, secret scanning) | OPS 5 | ☐ |
| I13 | Error messages and degraded-mode responses don't expose internal state | REL 4, REL 5 | ☐ |
| I14 | Compute uses hardware isolation where sensitive (Nitro enclaves, dedicated tenancy) | PERF 2 | ☐ |
| I15 | Data transfer paths minimize exposure (PrivateLink over public internet where possible) | COST 8, PERF 4 | ☐ |
| I16 | CDN/edge TLS termination points are identified and controlled | PERF 4 | ☐ |
| I17 | Service isolation ensures each service only accesses its own data boundary | REL 3 | ☐ |
| I18 | Detection capabilities identify data exfiltration patterns (VPC flow logs, Macie, GuardDuty) | SEC 4 | ☐ |

## D — Denial of Service Checklist

| # | Check | WAR Source | Pass? |
|---|-------|-----------|-------|
| D1 | DDoS protection is active (Shield Advanced, WAF rate rules, CloudFront) | SEC 5 | ☐ |
| D2 | Service quotas are monitored and right-sized (not at defaults, alarmed on approach) | REL 1 | ☐ |
| D3 | Network topology has no single points of failure (multi-AZ, redundant connectivity) | REL 2 | ☐ |
| D4 | Service architecture provides fault isolation (microservices can fail independently) | REL 3 | ☐ |
| D5 | Circuit breakers, timeouts, and retries with backoff prevent cascading failures | REL 4, REL 5 | ☐ |
| D6 | Monitoring detects capacity exhaustion before it causes outage | REL 6 | ☐ |
| D7 | Auto-scaling absorbs traffic spikes (configured, tested, with appropriate limits) | REL 7 | ☐ |
| D8 | Deployments use progressive rollout (canary/rolling) to prevent self-inflicted outage | OPS 6 | ☐ |
| D9 | Backups and DR enable recovery from destructive attacks within RTO | REL 9, REL 13 | ☐ |
| D10 | Fault isolation boundaries (cells, shuffle sharding) limit blast radius | REL 10 | ☐ |
| D11 | Redundancy and failover maintain availability during component failure | REL 11 | ☐ |
| D12 | Reliability is tested (game days, chaos engineering, load testing) | REL 12, PERF 5 | ☐ |
| D13 | CDN and edge caching absorb volumetric traffic at the perimeter | PERF 4 | ☐ |
| D14 | Throttling and queuing absorb request spikes (API Gateway, SQS, rate limiters) | COST 9 | ☐ |
| D15 | Resource sizing is sufficient (not under-provisioned to save cost at availability risk) | COST 6 | ☐ |
| D16 | Event management runbooks cover availability incidents with escalation paths | OPS 10 | ☐ |
| D17 | Operational readiness confirms failover and availability mechanisms are tested | OPS 7 | ☐ |
| D18 | Software is patched against known DoS protocol exploits | SUS 6 | ☐ |
| D19 | Systems fail-secure under load (don't disable WAF, rate limiting, or auth when overwhelmed) | REL 5, COST 6 | ☐ |

## E — Elevation of Privilege Checklist

| # | Check | WAR Source | Pass? |
|---|-------|-----------|-------|
| E1 | All access follows least privilege (no Admin/PowerUser policies on workload roles) | SEC 3 | ☐ |
| E2 | Identity management prevents privilege escalation paths (no iam:*, no self-escalation) | SEC 2 | ☐ |
| E3 | Compute is hardened against local escalation (no root, container breakout prevention, patched OS) | SEC 6 | ☐ |
| E4 | Application security testing catches authorization bypasses (BOLA, IDOR, injection) | SEC 11 | ☐ |
| E5 | Service boundaries enforce independent authorization (each service validates its own perms) | REL 3 | ☐ |
| E6 | SCPs and permission boundaries create hard guardrails that prevent policy escalation | COST 2 | ☐ |
| E7 | Cost monitoring detects unauthorized resource provisioning (new EC2, new accounts) | COST 3 | ☐ |
| E8 | Orphaned resources with excessive permissions are identified and removed | COST 4 | ☐ |
| E9 | Network segmentation prevents lateral movement after initial compromise | SEC 5, REL 2 | ☐ |
| E10 | Fault isolation limits scope — compromised component can't access adjacent cells | REL 10 | ☐ |
| E11 | Change authorization requires appropriate approval (no self-approve to production) | REL 8, OPS 6 | ☐ |
| E12 | CI/CD pipeline permissions prevent unauthorized actors from deploying | OPS 5 | ☐ |
| E13 | Separation of duties is enforced in organizational structure | OPS 2 | ☐ |
| E14 | Observability detects privilege abuse in real-time (unusual API calls, new permissions used) | OPS 4, OPS 8 | ☐ |
| E15 | DR "break glass" procedures are as strictly controlled as production access | REL 13 | ☐ |
| E16 | Degraded-mode operation doesn't relax authorization (systems fail-secure, not fail-open) | REL 5 | ☐ |
| E17 | Software is patched against known privilege escalation CVEs | SUS 6 | ☐ |
| E18 | Readiness review confirms RBAC and authorization controls are tested pre-launch | OPS 7 | ☐ |
