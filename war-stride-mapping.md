# AWS Well-Architected Framework → STRIDE Threat Model Mapping

---

## How to Use This Document (Start Here)

### What This Is

This document maps all 58 AWS Well-Architected Review (WAR) questions across 6 pillars to the STRIDE threat modeling framework. It serves as:

1. A **threat modeling guide** — bridging your WAR findings to actionable security improvements
2. A **code review framework** — Kiro uses this mapping to analyze your repository and identify gaps between your implementation and the WAR recommendations
3. A **JIRA task generator** — findings are structured so they can be directly exported as JIRA tickets

### How It Works in Kiro

When you open this workspace in Kiro, the workflow is:

```
┌─────────────────────────────────────────────────────────────────────┐
│                         WORKFLOW OVERVIEW                            │
├─────────────────────────────────────────────────────────────────────┤
│                                                                     │
│  1. INPUT SOURCES                                                   │
│     ├── This mapping document (WAR → STRIDE)                        │
│     ├── Your code repository (scanned by Kiro)                      │
│     └── [Optional] Confluence WAR export (if available)             │
│                                                                     │
│  2. ANALYSIS (Kiro performs)                                        │
│     ├── Scans code for patterns matching each WAR question          │
│     ├── Identifies gaps (WAR control not implemented in code)       │
│     ├── Maps gaps to STRIDE threat category                         │
│     └── Assesses severity based on threat relevance score           │
│                                                                     │
│  3. OUTPUT                                                          │
│     ├── Gap analysis report (per STRIDE category)                   │
│     ├── Actionable findings (JIRA-ready format)                     │
│     └── Prioritized remediation recommendations                     │
│                                                                     │
└─────────────────────────────────────────────────────────────────────┘
```

### Getting Started

**Step 1: Point Kiro at your code repo**
Open your infrastructure/application code alongside this workspace. Kiro will scan for:
- IaC (CDK, Terraform, CloudFormation) — checks security controls, network config, IAM policies
- Application code — checks auth patterns, input validation, logging, error handling
- CI/CD config — checks pipeline integrity, signing, deployment controls
- Config files — checks encryption settings, access controls, retention policies

**Step 2: Ask Kiro to run the review**
Prompt examples:
- "Review my code against the STRIDE mapping for the Security pillar"
- "What WAR questions does my codebase fail to address?"
- "Generate JIRA tasks for the gaps in my Denial of Service defenses"
- "Compare my Confluence WAR export against what's actually implemented in code"

**Step 3: Review findings and export to JIRA**
Kiro will generate findings in JIRA-ready format (see "JIRA Task Output Format" section below).

### JIRA Task Output Format

Every finding Kiro generates follows this structure so it can be directly pasted or API-pushed into JIRA:

```
┌─────────────────────────────────────────────────────────────────────┐
│ JIRA TASK TEMPLATE                                                  │
├─────────────────────────────────────────────────────────────────────┤
│                                                                     │
│ Summary:    [STRIDE-X] <concise gap description>                    │
│ Type:       Task | Story | Bug (depends on severity)                │
│ Priority:   Critical | High | Medium | Low                          │
│ Labels:     war-review, stride-<category>, <pillar>                 │
│ Epic:       Well-Architected Remediation                            │
│                                                                     │
│ Description:                                                        │
│ ┌─────────────────────────────────────────────────────────────────┐ │
│ │ **WAR Question:** SEC X / REL X / OPS X / etc.                  │ │
│ │ **STRIDE Category:** Spoofing|Tampering|Repudiation|etc.        │ │
│ │ **Threat:** What can go wrong (specific to your system)         │ │
│ │ **Current State:** What code/config exists today                │ │
│ │ **Gap:** What's missing or misconfigured                        │ │
│ │ **Recommendation:** Specific fix with AWS service reference     │ │
│ │ **Code Location:** file:line or module reference                │ │
│ │ **AWS Services:** Relevant services to implement the fix        │ │
│ │ **Acceptance Criteria:**                                        │ │
│ │   - [ ] Criterion 1 (testable)                                  │ │
│ │   - [ ] Criterion 2 (testable)                                  │ │
│ │   - [ ] Criterion 3 (testable)                                  │ │
│ │ **Effort Estimate:** T-shirt size (S/M/L/XL)                   │ │
│ │ **Risk if Not Addressed:** Impact statement                     │ │
│ └─────────────────────────────────────────────────────────────────┘ │
│                                                                     │
│ Linked Issues: (other tasks that address the same WAR question)     │
│ Components:    (affected system components)                         │
│                                                                     │
└─────────────────────────────────────────────────────────────────────┘
```

**Priority Mapping:**
| STRIDE Relevance in Mapping | Code Gap Severity | JIRA Priority |
|----------------------------|-------------------|---------------|
| ●● Critical | Control completely absent | Critical |
| ●● Critical | Control partially implemented | High |
| ● High | Control completely absent | High |
| ● High | Control partially implemented | Medium |
| ○ Medium | Control completely absent | Medium |
| ○ Medium | Control partially implemented | Low |

**Label Convention:**
- `war-review` — all tasks from this process
- `stride-spoofing`, `stride-tampering`, `stride-repudiation`, `stride-information-disclosure`, `stride-dos`, `stride-elevation-of-privilege` — STRIDE category
- `sec-pillar`, `rel-pillar`, `ops-pillar`, `perf-pillar`, `cost-pillar`, `sus-pillar` — WAR pillar
- `war-sec-02`, `war-rel-03`, etc. — specific WAR question reference

### Example JIRA Tasks (What Kiro Generates)

**Example 1: Critical finding**
```
Summary:     [STRIDE-E] Lambda functions use wildcard IAM policies
Type:        Bug
Priority:    Critical
Labels:      war-review, stride-elevation-of-privilege, sec-pillar, war-sec-03
Epic:        Well-Architected Remediation

Description:
**WAR Question:** SEC 3 — How do you manage permissions for people and machines?
**STRIDE Category:** Elevation of Privilege (●● Critical)
**Threat:** Compromised Lambda function has full account access via Action: "*"
**Current State:** `infra/lambda-stack.ts:47` grants `PolicyStatement({ actions: ["*"] })`
**Gap:** No least-privilege scoping — any function compromise escalates to full account
**Recommendation:** Replace wildcard with specific actions per function's needs. Use
  IAM Access Analyzer to determine actual used permissions.
**Code Location:** infra/lambda-stack.ts:47
**AWS Services:** IAM Access Analyzer, IAM Policy Simulator
**Acceptance Criteria:**
  - [ ] All Lambda roles use specific action lists (no wildcards)
  - [ ] IAM Access Analyzer shows no unused permissions after 30 days
  - [ ] Resource ARNs are scoped (no Resource: "*" on write actions)
**Effort Estimate:** M
**Risk if Not Addressed:** Full account takeover via single function compromise
```

**Example 2: High finding**
```
Summary:     [STRIDE-I] S3 bucket lacks encryption configuration
Type:        Task
Priority:    High
Labels:      war-review, stride-information-disclosure, sec-pillar, war-sec-08
Epic:        Well-Architected Remediation

Description:
**WAR Question:** SEC 8 — How do you protect your data at rest?
**STRIDE Category:** Information Disclosure (●● Critical)
**Threat:** Data exposure if bucket contents accessed by unauthorized party
**Current State:** `infra/storage.tf:12` creates bucket without encryption block
**Gap:** No server-side encryption configured; data stored in plaintext
**Recommendation:** Add SSE-KMS with dedicated CMK. Enable bucket key for cost
  optimization.
**Code Location:** infra/storage.tf:12
**AWS Services:** KMS, S3 SSE-KMS, S3 Bucket Key
**Acceptance Criteria:**
  - [ ] All S3 buckets have SSE-KMS encryption enabled
  - [ ] KMS key policy restricts decrypt to intended consumers
  - [ ] Bucket policy denies PutObject without encryption header
**Effort Estimate:** S
**Risk if Not Addressed:** Data breach exposure of stored objects
```

**Example 3: Medium finding from Reliability pillar**
```
Summary:     [STRIDE-D] No circuit breaker on downstream API calls
Type:        Story
Priority:    Medium
Labels:      war-review, stride-dos, rel-pillar, war-rel-04
Epic:        Well-Architected Remediation

Description:
**WAR Question:** REL 4 — How do you design interactions to prevent failures?
**STRIDE Category:** Denial of Service (●● Critical)
**Threat:** Slow downstream dependency causes cascading timeout across all services
**Current State:** `src/services/payment-client.ts:23` uses direct HTTP call with
  30s timeout but no circuit breaker or bulkhead
**Gap:** No circuit breaker pattern; downstream failure cascades to caller
**Recommendation:** Implement circuit breaker (e.g., opossum, resilience4j) with
  fallback. Add timeout + retry with exponential backoff.
**Code Location:** src/services/payment-client.ts:23
**AWS Services:** App Mesh (circuit breaker policy), CloudWatch (trip metrics)
**Acceptance Criteria:**
  - [ ] Circuit breaker opens after 5 consecutive failures (configurable)
  - [ ] Fallback returns cached/degraded response during open state
  - [ ] CloudWatch alarm fires when circuit opens
  - [ ] Timeout set to 3s (not 30s)
**Effort Estimate:** M
**Risk if Not Addressed:** Single dependency failure takes down entire service
```

### Using with Confluence WAR Exports

If you have a Confluence export from a previous Well-Architected Review:

1. **Export format:** HTML or PDF export from Confluence, placed in this workspace
2. **What Kiro does:** Extracts findings/recommendations from the export, maps them to WAR question IDs, then compares against your code
3. **The value:** Identifies which Confluence recommendations have been implemented (evidence in code) vs. still outstanding (no code evidence)
4. **Output:** JIRA tasks only for gaps that remain unaddressed in code

### Using with Code Repository Directly (No Confluence)

If you don't have a Confluence export:

1. **Kiro scans your code** against each WAR question in this mapping
2. **Pattern matching:** Looks for presence/absence of controls (encryption config, IAM policies, logging setup, circuit breakers, etc.)
3. **Generates findings:** Where code doesn't implement the control implied by a WAR question
4. **Output:** Same JIRA-ready format with code location references

### Reconciliation Modes

| Mode | Input | Use When |
|------|-------|----------|
| **Code-only review** | This mapping + code repo | No prior WAR review exists; doing a fresh assessment |
| **Confluence reconciliation** | This mapping + Confluence export + code repo | Prior WAR review exists; checking implementation status |
| **Targeted STRIDE review** | This mapping + code repo + specific STRIDE category | Focused threat model (e.g., "just check Elevation of Privilege") |
| **Pillar-specific review** | This mapping + code repo + specific pillar | Focused WAR pillar (e.g., "just check Reliability") |

---

## Complete Cross-Pillar Mapping

## STRIDE Categories Reference

| Category | Threat | Security Property Violated |
|----------|--------|---------------------------|
| **S** - Spoofing | Impersonating a user or system | Authenticity |
| **T** - Tampering | Modifying data or code | Integrity |
| **R** - Repudiation | Denying an action was performed | Non-repudiability |
| **I** - Information Disclosure | Exposing data to unauthorized parties | Confidentiality |
| **D** - Denial of Service | Making system unavailable | Availability |
| **E** - Elevation of Privilege | Gaining unauthorized access levels | Authorization |

---

# Pillar 1: Security

### SEC 1: How do you securely operate your workload?
**Area:** Security Foundations

| STRIDE | Relevance | Rationale |
|--------|-----------|-----------|
| S | ● High | Operational security controls prevent impersonation of legitimate operators |
| T | ● High | Secure operations ensure configuration and runtime integrity |
| R | ● High | Operational logging and audit trails ensure accountability |
| I | ○ Medium | Operational controls limit exposure of sensitive operational data |
| D | ○ Medium | Operational practices include availability protections |
| E | ● High | Separation of duties and least privilege for operators |

---

### SEC 2: How do you manage identities for people and machines?
**Area:** Identity and Access Management

| STRIDE | Relevance | Rationale |
|--------|-----------|-----------|
| **S** | ●● Critical | Direct defense — prevents impersonation of users and services |
| T | ○ Medium | Authenticated identities are prerequisite to tamper-proof operations |
| R | ● High | Identity binding enables attributing actions to specific actors |
| I | ○ Medium | Identity controls gate access to sensitive data |
| D | ○ Low | Indirect — compromised identities can enable DoS |
| **E** | ●● Critical | Identity confusion/compromise is primary path to privilege escalation |

---

### SEC 3: How do you manage permissions for people and machines?
**Area:** Identity and Access Management

| STRIDE | Relevance | Rationale |
|--------|-----------|-----------|
| S | ● High | Permission systems validate identity grants |
| T | ● High | Write permissions control who can modify resources |
| R | ○ Medium | Permission changes should be logged |
| I | ● High | Read permissions control access to confidential data |
| D | ○ Medium | Overly broad permissions can enable destructive actions |
| **E** | ●● Critical | Direct defense — least privilege, permission boundaries |

---

### SEC 4: How do you detect and investigate security events?
**Area:** Detection

| STRIDE | Relevance | Rationale |
|--------|-----------|-----------|
| S | ● High | Detection identifies impersonation attempts |
| T | ● High | Integrity monitoring detects unauthorized changes |
| **R** | ●● Critical | Direct defense — logging and audit trails make actions provable |
| I | ● High | Detects data exfiltration patterns |
| D | ● High | Monitoring detects anomalous traffic |
| E | ● High | Behavioral detection identifies unusual privilege usage |

---

### SEC 5: How do you protect your network resources?
**Area:** Infrastructure Protection

| STRIDE | Relevance | Rationale |
|--------|-----------|-----------|
| S | ● High | Prevents IP/DNS spoofing and MITM attacks |
| T | ● High | Network encryption prevents packet modification |
| R | ○ Low | Network logs provide some audit trail |
| **I** | ●● Critical | Segmentation and encryption prevent eavesdropping |
| **D** | ●● Critical | Direct defense — DDoS protection, rate limiting, WAF |
| E | ● High | Network boundaries prevent lateral movement |

---

### SEC 6: How do you protect your compute resources?
**Area:** Infrastructure Protection

| STRIDE | Relevance | Rationale |
|--------|-----------|-----------|
| S | ○ Medium | Hardened compute reduces impersonation attack surface |
| **T** | ●● Critical | Immutable infrastructure, patching, integrity checks |
| R | ○ Low | Not primarily about non-repudiation |
| I | ● High | Prevents memory dumps, side-channel attacks |
| D | ● High | Resource protection ensures compute availability |
| **E** | ●● Critical | Prevents local privilege escalation, container breakout |

---

### SEC 7: How do you classify your data?
**Area:** Data Protection

| STRIDE | Relevance | Rationale |
|--------|-----------|-----------|
| S | ○ Low | Not directly related to identity |
| T | ○ Medium | Informs integrity requirements per sensitivity |
| R | ○ Medium | Determines which changes require audit trails |
| **I** | ●● Critical | Enables appropriate confidentiality controls |
| D | ○ Low | Indirect relationship |
| E | ○ Medium | Informs access control granularity |

---

### SEC 8: How do you protect your data at rest?
**Area:** Data Protection

| STRIDE | Relevance | Rationale |
|--------|-----------|-----------|
| S | ○ Low | Not primarily about identity |
| **T** | ● High | Integrity controls detect unauthorized modification |
| R | ○ Medium | Access logging supports non-repudiation |
| **I** | ●● Critical | Encryption, key management prevent unauthorized exposure |
| D | ○ Low | Indirect relationship |
| E | ○ Medium | Key management prevents unauthorized decryption |

---

### SEC 9: How do you protect your data in transit?
**Area:** Data Protection

| STRIDE | Relevance | Rationale |
|--------|-----------|-----------|
| **S** | ● High | TLS/mutual auth prevents MITM and impersonation |
| **T** | ●● Critical | Encryption and integrity checks prevent in-flight modification |
| R | ○ Low | Not primarily about accountability |
| **I** | ●● Critical | TLS prevents eavesdropping on data in motion |
| D | ○ Low | Indirect |
| E | ○ Medium | Prevents credential interception |

---

### SEC 10: How do you anticipate, respond to, and recover from incidents?
**Area:** Incident Response

| STRIDE | Relevance | Rationale |
|--------|-----------|-----------|
| S | ● High | Handles compromised identity scenarios |
| T | ● High | Containment and restoration from unauthorized changes |
| R | ● High | Forensics and evidence preservation |
| I | ● High | Breach response limits ongoing exposure |
| D | ● High | Recovery procedures restore availability |
| E | ● High | Revokes escalated access, root cause analysis |

---

### SEC 11: How do you incorporate and validate security properties of applications throughout the SDLC?
**Area:** Application Security

| STRIDE | Relevance | Rationale |
|--------|-----------|-----------|
| S | ● High | Validates authentication mechanisms |
| **T** | ●● Critical | Prevents injection, validates input, ensures code integrity |
| R | ○ Medium | Application logging design supports accountability |
| I | ● High | Catches data leakage vulnerabilities |
| D | ○ Medium | Includes resilience patterns |
| **E** | ●● Critical | Catches authorization bypasses, injection, privilege escalation |

---

# Pillar 2: Reliability

### REL 1: How do you manage Service Quotas and constraints?
**Area:** Foundations

| STRIDE | Relevance | Rationale |
|--------|-----------|-----------|
| S | ○ Low | Not directly related |
| T | ○ Low | Not directly related |
| R | ○ Low | Not directly related |
| I | ○ Low | Not directly related |
| **D** | ●● Critical | Direct defense — quota management prevents resource exhaustion attacks |
| E | ● High | Quotas limit blast radius — compromised credentials can't spin up unlimited resources |

---

### REL 2: How do you plan your network topology?
**Area:** Foundations

| STRIDE | Relevance | Rationale |
|--------|-----------|-----------|
| S | ● High | Network topology defines trust zones — what can authenticate to what (VPC peering, PrivateLink) |
| T | ○ Medium | Topology influences data path integrity and which paths can be intercepted |
| R | ○ Medium | Network flow logs tied to topology provide attributable evidence |
| **I** | ● High | Topology determines data flow exposure — public vs private subnets, VPC endpoints |
| **D** | ●● Critical | Topology redundancy (multi-AZ, multi-region) prevents single points of failure |
| E | ● High | Segmentation via VPCs/subnets limits lateral movement and blast radius of compromises |

---

### REL 3: How do you design your workload service architecture?
**Area:** Workload Architecture

| STRIDE | Relevance | Rationale |
|--------|-----------|-----------|
| **S** | ● High | Service boundaries require service-to-service authentication (mTLS, IAM roles, service mesh) |
| T | ● High | Service contracts (API schemas, input validation) enforce interface integrity |
| R | ● High | Per-service logging enables fine-grained attribution of actions |
| I | ● High | Service isolation limits data exposure — each service owns its data boundary |
| **D** | ●● Critical | SOA/microservices provide fault isolation, independent scaling, and circuit breaking |
| **E** | ●● Critical | Service boundaries ARE authorization boundaries — each service enforces its own access policy |

---

### REL 4: How do you design interactions in a distributed system to prevent failures?
**Area:** Workload Architecture

| STRIDE | Relevance | Rationale |
|--------|-----------|-----------|
| S | ○ Medium | Timeout/circuit breaker patterns prevent resource exhaustion from spoofed flood requests |
| T | ● High | Idempotency prevents duplicate/replay attacks from corrupting state; contract validation ensures integrity |
| R | ○ Medium | Distributed tracing across interactions enables attribution |
| I | ○ Medium | Failed interactions should not leak error details or internal state to callers |
| **D** | ●● Critical | Direct defense — circuit breakers, timeouts, throttling prevent cascading failures from attacks |
| E | ○ Medium | Rate limiting per-caller prevents single actor from abusing API capacity |

---

### REL 5: How do you design interactions in a distributed system to mitigate or withstand failures?
**Area:** Workload Architecture

| STRIDE | Relevance | Rationale |
|--------|-----------|-----------|
| S | ○ Medium | Fallback paths must still authenticate requests — degraded mode can't skip auth |
| T | ● High | Graceful degradation must maintain data consistency and not corrupt state during failover |
| R | ○ Medium | Degraded-mode operations still need logging for accountability |
| I | ○ Medium | Failover must not expose data to broader audiences than normal mode |
| **D** | ●● Critical | Direct defense — retry with backoff, bulkheads, queue-based load leveling, failover |
| E | ○ Medium | Degraded-mode must not relax authorization (fail-secure, not fail-open) |

---

### REL 6: How do you monitor workload resources?
**Area:** Change Management

| STRIDE | Relevance | Rationale |
|--------|-----------|-----------|
| S | ● High | Monitoring detects anomalous access patterns that indicate identity compromise |
| **T** | ● High | Monitoring detects unauthorized configuration/state changes in real-time |
| **R** | ● High | Monitoring metrics and logs provide continuous audit evidence of system behavior |
| I | ○ Medium | Monitoring data itself must be protected; can reveal architecture to attackers |
| **D** | ●● Critical | Proactive detection of resource exhaustion, capacity limits, and availability degradation |
| E | ● High | Detects unusual resource access patterns that indicate privilege abuse |

---

### REL 7: How do you design your workload to adapt to changes in demand?
**Area:** Change Management

| STRIDE | Relevance | Rationale |
|--------|-----------|-----------|
| S | ○ Low | Not directly related |
| T | ○ Low | Not directly related |
| R | ○ Low | Not directly related |
| I | ○ Low | Not directly related |
| **D** | ●● Critical | Direct defense — auto-scaling absorbs volumetric attacks and load spikes |
| E | ○ Low | Not directly related |

---

### REL 8: How do you implement change?
**Area:** Change Management

| STRIDE | Relevance | Rationale |
|--------|-----------|-----------|
| S | ○ Medium | Change controls validate who is making changes |
| **T** | ●● Critical | Controlled changes prevent unauthorized modification of production systems |
| **R** | ● High | Change logs provide accountability for all modifications |
| I | ○ Medium | Change processes protect sensitive deployment configurations |
| D | ● High | Controlled rollouts prevent destabilizing deployments |
| E | ● High | Change authorization prevents unauthorized production access |

---

### REL 9: How do you back up data?
**Area:** Failure Management

| STRIDE | Relevance | Rationale |
|--------|-----------|-----------|
| S | ○ Low | Not directly related |
| **T** | ● High | Backups enable recovery from data tampering/corruption |
| R | ○ Medium | Backup logs support forensic timelines |
| I | ● High | Backups must be encrypted — they contain sensitive data |
| **D** | ●● Critical | Direct defense — backups enable recovery from destructive attacks |
| E | ○ Medium | Backup access controls prevent unauthorized restoration |

---

### REL 10: How do you use fault isolation to protect your workload?
**Area:** Failure Management

| STRIDE | Relevance | Rationale |
|--------|-----------|-----------|
| S | ○ Medium | Isolation boundaries require re-authentication when crossing (zero trust) |
| T | ● High | Isolation contains blast radius of tampering — corrupted component can't spread |
| R | ○ Medium | Isolated components have independent audit trails |
| **I** | ● High | Isolation boundaries ARE data boundaries — breach in one cell doesn't expose adjacent cells |
| **D** | ●● Critical | Direct defense — fault boundaries prevent cascading outages (cell-based architecture) |
| **E** | ● High | Isolation limits scope of privilege escalation — compromised component has bounded permissions |

---

### REL 11: How do you design your workload to withstand component failures?
**Area:** Failure Management

| STRIDE | Relevance | Rationale |
|--------|-----------|-----------|
| S | ○ Medium | Redundant components must independently validate authenticity (not share credentials) |
| T | ● High | Redundancy enables recovery from corrupted components; consensus protocols detect tampering |
| R | ○ Medium | Failover logs provide evidence of which component handled which request |
| I | ○ Medium | Redundant data stores must maintain same encryption/access controls |
| **D** | ●● Critical | Direct defense — redundancy, failover, and multi-AZ maintain availability |
| E | ○ Medium | Standby components must not run with elevated privileges compared to primary |

---

### REL 12: How do you test reliability?
**Area:** Failure Management

| STRIDE | Relevance | Rationale |
|--------|-----------|-----------|
| S | ○ Medium | Chaos tests should validate that auth still works under degraded conditions |
| T | ● High | Chaos testing validates recovery from data corruption and validates integrity checks work |
| R | ○ Medium | Test results and game day reports provide evidence of system behavior |
| I | ○ Medium | Failure testing should validate that degraded mode doesn't leak data |
| **D** | ●● Critical | Game days and chaos engineering validate resilience to DoS and resource exhaustion |
| E | ○ Medium | Tests should validate that failover doesn't escalate privileges (fail-secure) |

---

### REL 13: How do you plan for disaster recovery (DR)?
**Area:** Failure Management

| STRIDE | Relevance | Rationale |
|--------|-----------|-----------|
| S | ○ Medium | DR processes must validate identity before granting emergency access |
| T | ● High | DR enables recovery from large-scale data tampering (ransomware, wiper malware) |
| R | ● High | DR plans must preserve forensic evidence and maintain chain of custody |
| **I** | ● High | DR sites must maintain same confidentiality controls; cross-region replication must encrypt |
| **D** | ●● Critical | Direct defense — DR ensures availability after catastrophic events (region failure, destructive attacks) |
| E | ● High | DR "break glass" procedures must be as strictly controlled as production access |

---

# Pillar 3: Operational Excellence

### OPS 1: How do you determine what your priorities are?
**Area:** Organization

| STRIDE | Relevance | Rationale |
|--------|-----------|-----------|
| S | ○ Medium | Prioritization determines investment in identity/authentication controls |
| T | ○ Medium | Priorities determine whether integrity controls receive engineering effort |
| R | ○ Medium | Audit/compliance requirements must be prioritized to be implemented |
| I | ○ Medium | Data protection investment depends on organizational priority |
| D | ○ Medium | Availability SLAs are set by business priority decisions |
| E | ○ Medium | Security investment (least privilege, access controls) competes with feature velocity |

*Governance sets the ceiling for all STRIDE defenses — under-prioritized security = systemic weakness.*

---

### OPS 2: How do you structure your organization to support your business outcomes?
**Area:** Organization

| STRIDE | Relevance | Rationale |
|--------|-----------|-----------|
| S | ○ Medium | Defined roles prevent social engineering via ambiguous authority |
| T | ● High | Clear ownership enforces who is authorized to modify systems (separation of duties) |
| R | ● High | Defined ownership = clear accountability — you know who did what |
| I | ○ Medium | Need-to-know requires clear team boundaries for data access |
| D | ○ Medium | Ownership clarity prevents neglect of availability-critical systems |
| **E** | ● High | Separation of duties and defined authority prevent unauthorized decisions and insider privilege abuse |

---

### OPS 3: How does your organizational culture support your business outcomes?
**Area:** Organization

| STRIDE | Relevance | Rationale |
|--------|-----------|-----------|
| S | ○ Medium | Security-aware culture reduces susceptibility to social engineering/phishing |
| T | ○ Medium | Culture of code review and peer verification prevents unauthorized modifications |
| R | ● High | Blame-free culture encourages incident reporting — strengthens accountability chain |
| I | ○ Medium | Culture of data handling awareness reduces accidental information leaks |
| D | ○ Medium | Operational discipline culture reduces human-caused outages |
| E | ○ Medium | Culture that respects access boundaries reduces insider threat |

*Security culture is a force multiplier — weak culture undermines all technical controls.*

---

### OPS 4: How do you implement observability in your workload?
**Area:** Prepare

| STRIDE | Relevance | Rationale |
|--------|-----------|-----------|
| S | ● High | Observability detects impersonation through anomaly detection |
| T | ● High | Tracing and logs detect unauthorized modifications |
| **R** | ●● Critical | Comprehensive observability (logs, traces, metrics) provides irrefutable evidence |
| I | ○ Medium | Observability data itself requires protection |
| D | ● High | Early detection of degradation prevents full outage |
| E | ● High | Traces reveal unauthorized access paths |

---

### OPS 5: How do you reduce defects, ease remediation, and improve flow into production?
**Area:** Prepare

| STRIDE | Relevance | Rationale |
|--------|-----------|-----------|
| **S** | ● High | Pipeline identity (signed commits, verified build agents) prevents supply chain spoofing |
| **T** | ●● Critical | CI/CD pipelines with integrity checks, signed artifacts prevent malicious code injection |
| R | ● High | Pipeline logs and artifact provenance provide deployment accountability |
| I | ● High | Build artifacts, environment variables, and registries may contain/expose secrets |
| D | ○ Medium | Faster deployments enable quicker patches for DoS vulnerabilities |
| **E** | ● High | Pipeline permissions (who can merge, deploy, approve) prevent unauthorized privilege escalation |

---

### OPS 6: How do you mitigate deployment risks?
**Area:** Prepare

| STRIDE | Relevance | Rationale |
|--------|-----------|-----------|
| S | ○ Medium | Deployment approval validates deployer identity |
| **T** | ●● Critical | Rollback capabilities undo unauthorized or corrupted deployments |
| R | ● High | Deployment audit trails track who changed what |
| I | ○ Medium | Blue/green deploys limit exposure of misconfigured services |
| **D** | ● High | Canary/rolling deploys prevent deployment-caused outages |
| E | ● High | Deployment authorization gates prevent unauthorized production changes |

---

### OPS 7: How do you know that you are ready to support a workload?
**Area:** Prepare

| STRIDE | Relevance | Rationale |
|--------|-----------|-----------|
| S | ● High | Readiness reviews confirm authentication mechanisms are configured and tested |
| T | ● High | Readiness checklists validate integrity controls (immutable infra, signing) are in place |
| R | ● High | Checklists confirm logging, audit trails, and monitoring readiness |
| I | ● High | Readiness confirms encryption, data protection, and access controls before go-live |
| **D** | ● High | Operational readiness ensures availability SLAs, runbooks, and failover are tested |
| E | ● High | Confirms RBAC, least privilege, and authorization controls are properly configured |

*Readiness is the gate that validates ALL STRIDE defenses are operational before production.*

---

### OPS 8: How do you utilize workload observability in your organization?
**Area:** Operate

| STRIDE | Relevance | Rationale |
|--------|-----------|-----------|
| S | ● High | Real-time observability detects active impersonation |
| T | ● High | Anomaly detection identifies unauthorized modifications |
| **R** | ●● Critical | Continuous observability provides ongoing evidence chain |
| I | ○ Medium | Dashboards must not expose sensitive data |
| D | ● High | Alert-driven response prevents prolonged outages |
| E | ● High | Observability reveals privilege abuse in real-time |

---

### OPS 9: How do you understand the health of your operations?
**Area:** Operate

| STRIDE | Relevance | Rationale |
|--------|-----------|-----------|
| S | ○ Medium | Operations health metrics detect unauthorized operator activity |
| T | ○ Medium | Operational KPIs reveal unplanned changes |
| R | ○ Medium | Operations metrics provide system behavior evidence |
| I | ○ Low | Not directly related |
| **D** | ● High | Operations health monitoring is early warning for availability issues |
| E | ○ Low | Not directly related |

---

### OPS 10: How do you manage workload and operations events?
**Area:** Operate

| STRIDE | Relevance | Rationale |
|--------|-----------|-----------|
| S | ○ Medium | Event response may include identity compromise scenarios |
| T | ● High | Runbooks for tampering scenarios enable rapid response |
| R | ● High | Event documentation creates accountability records |
| I | ○ Medium | Event handling may involve data breach procedures |
| **D** | ●● Critical | Event management directly handles availability incidents |
| E | ○ Medium | Events may include privilege escalation incidents |

---

### OPS 11: How do you evolve operations?
**Area:** Evolve

| STRIDE | Relevance | Rationale |
|--------|-----------|-----------|
| S | ○ Medium | Post-incident reviews identify authentication gaps to close |
| T | ● High | Lessons learned from tampering incidents drive integrity improvements |
| R | ○ Medium | Feedback loops improve audit and logging completeness |
| I | ○ Medium | Data breach post-mortems drive data protection improvements |
| D | ● High | Post-incident improvements (chaos engineering, SLA reviews) strengthen availability |
| E | ○ Medium | Access control reviews based on incidents reduce privilege creep |

*Continuous improvement is the feedback loop that hardens ALL STRIDE defenses over time.*

---

# Pillar 4: Performance Efficiency

### PERF 1: How do you select appropriate cloud resources and architecture patterns?
**Area:** Architecture Selection

| STRIDE | Relevance | Rationale |
|--------|-----------|-----------|
| S | ○ Medium | Managed services provide built-in identity integration vs DIY auth |
| T | ○ Medium | Managed services include built-in integrity (checksums, versioning) vs self-managed |
| R | ○ Medium | Architecture patterns determine audit capability (serverless = automatic CloudTrail) |
| I | ○ Medium | Resource selection affects available encryption and data isolation options |
| **D** | ● High | Right-sized architecture avoids performance-based unavailability under load |
| E | ○ Medium | Managed services enforce built-in access controls vs custom implementations |

---

### PERF 2: How do you select and use compute resources in your workload?
**Area:** Compute and Hardware

| STRIDE | Relevance | Rationale |
|--------|-----------|-----------|
| S | ○ Low | Not directly related |
| T | ● High | Compute selection determines hardware security (Nitro, TPM, secure enclaves for integrity) |
| R | ○ Low | Not directly related |
| I | ● High | Nitro enclaves, confidential computing prevent memory-level data exposure |
| **D** | ● High | Properly sized compute prevents performance degradation under load (slowloris, resource exhaustion) |
| E | ○ Medium | Hardware-level isolation (Nitro hypervisor) prevents cross-tenant privilege escalation |

---

### PERF 3: How do you store, manage, and access data in your workload?
**Area:** Data Management

| STRIDE | Relevance | Rationale |
|--------|-----------|-----------|
| S | ○ Medium | Database/storage auth mechanisms vary significantly by service selection |
| T | ● High | Data store selection determines integrity guarantees (ACID, WAL, checksums, versioning) |
| R | ● High | Storage selection determines audit capabilities (S3 access logs, DynamoDB Streams, RDS audit) |
| I | ● High | Storage selection determines encryption options (SSE-KMS, client-side, column-level) |
| **D** | ● High | Proper data tier selection prevents I/O bottleneck-driven unavailability |
| E | ○ Medium | Database-level access control (row-level security, IAM auth) varies by service |

---

### PERF 4: How do you select and configure networking resources?
**Area:** Networking and Content Delivery

| STRIDE | Relevance | Rationale |
|--------|-----------|-----------|
| S | ● High | CDN and edge config affects trust boundary enforcement; CloudFront signed URLs = auth at edge |
| T | ● High | Edge/CDN config determines whether content integrity is validated (origin shield, signed content) |
| R | ○ Medium | CDN access logs provide geographic and temporal audit trail |
| I | ● High | Network config determines TLS termination points and where data is decrypted/exposed |
| **D** | ●● Critical | CDN, edge caching, Global Accelerator, and Shield absorb volumetric DDoS attacks |
| E | ○ Medium | Edge authorization (Lambda@Edge, CloudFront Functions) can enforce access at the perimeter |

---

### PERF 5: What process do you use to support more performance efficiency?
**Area:** Process and Culture

| STRIDE | Relevance | Rationale |
|--------|-----------|-----------|
| S | ○ Low | Not directly related |
| T | ○ Medium | Performance baselines help detect anomalous behavior that indicates tampering |
| R | ○ Low | Not directly related |
| I | ○ Low | Not directly related |
| **D** | ● High | Load/performance testing reveals DoS vulnerabilities and breaking points before attackers find them |
| E | ○ Medium | Performance testing should include auth overhead — under-tested auth is often bypassed at load |

---

# Pillar 5: Cost Optimization

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

---

# Pillar 6: Sustainability

### SUS 1: How do you select Regions for your workload?
**Area:** Region Selection

| STRIDE | Relevance | Rationale |
|--------|-----------|-----------|
| S | ○ Low | Not directly related |
| T | ○ Low | Not directly related |
| R | ○ Low | Not directly related |
| I | ● High | Region selection affects data residency and sovereignty compliance |
| D | ○ Medium | Multi-region choices affect availability |
| E | ○ Low | Not directly related |

---

### SUS 2: How do you align cloud resources to your demand?
**Area:** Alignment to Demand

| STRIDE | Relevance | Rationale |
|--------|-----------|-----------|
| S | ○ Low | Not directly related |
| T | ○ Low | Not directly related |
| R | ○ Low | Not directly related |
| I | ○ Low | Not directly related |
| D | ● High | Right-sizing prevents capacity-based unavailability |
| E | ○ Low | Not directly related |

---

### SUS 3: How do you take advantage of software and architecture patterns to support sustainability goals?
**Area:** Software and Architecture

| STRIDE | Relevance | Rationale |
|--------|-----------|-----------|
| S | ○ Medium | Removing unused components reduces attack surface for impersonation endpoints |
| T | ○ Medium | Consolidating components reduces the number of things that need integrity monitoring |
| R | ○ Low | Not directly related |
| I | ○ Medium | Decommissioning idle components removes potential data exposure points |
| D | ● High | Efficient architectures with load smoothing are more resilient to traffic spikes |
| E | ○ Medium | Fewer components = smaller attack surface for privilege escalation |

*Reducing unused components is both sustainable AND reduces attack surface.*

---

### SUS 4: How do you take advantage of data management policies and patterns to support sustainability goals?
**Area:** Data Management

| STRIDE | Relevance | Rationale |
|--------|-----------|-----------|
| S | ○ Low | Not directly related |
| T | ○ Medium | Data deduplication and consolidation reduces locations that need integrity verification |
| R | ○ Medium | Data lifecycle policies must preserve audit logs for compliance retention periods |
| **I** | ●● Critical | Data lifecycle policies determine when sensitive data is deleted — shorter retention = smaller breach window; data that doesn't exist can't be exfiltrated |
| D | ○ Medium | Reducing over-provisioned storage frees capacity for production workloads |
| E | ○ Medium | Fewer data copies = fewer access paths to govern |

---

### SUS 5: How do you select and use cloud hardware and services to support sustainability goals?
**Area:** Hardware and Services

| STRIDE | Relevance | Rationale |
|--------|-----------|-----------|
| S | ○ Low | Not directly related |
| T | ○ Medium | Managed services include built-in integrity controls (patching, hardening) |
| R | ○ Low | Not directly related |
| I | ○ Medium | Managed services include built-in encryption and access controls |
| D | ○ Medium | Managed services handle availability/scaling without customer effort |
| E | ○ Medium | Using managed services shifts privilege escalation prevention to AWS (shared responsibility) |

---

### SUS 6: How do your organizational processes support your sustainability goals?
**Area:** Process and Culture

| STRIDE | Relevance | Rationale |
|--------|-----------|-----------|
| S | ○ Medium | Up-to-date software patches authentication bypass vulnerabilities |
| **T** | ● High | Keeping software current patches known code injection and integrity vulnerabilities |
| R | ○ Low | Not directly related |
| I | ○ Medium | Patching closes known data disclosure vulnerabilities (CVEs) |
| D | ● High | Up-to-date software patches known DoS exploits and protocol vulnerabilities |
| **E** | ● High | Patching closes known privilege escalation vulnerabilities (the #1 CVE category) |

---

# Cross-Pillar Summary Matrix

## High-Relevance Questions by STRIDE Category

### S — Spoofing (Authenticity)
| Question | Relevance |
|----------|-----------|
| SEC 2 - Identity Management | ●● Critical |
| SEC 9 - Data in Transit | ● High |
| SEC 1 - Secure Operations | ● High |
| SEC 3 - Permissions | ● High |
| SEC 4 - Detection | ● High |
| SEC 5 - Network Protection | ● High |
| SEC 11 - Application Security | ● High |
| SEC 10 - Incident Response | ● High |
| REL 2 - Network Topology | ● High |
| REL 3 - Service Architecture | ● High |
| OPS 4 - Observability | ● High |
| OPS 5 - CI/CD Pipeline | ● High |
| OPS 7 - Readiness | ● High |
| OPS 8 - Workload Observability | ● High |
| PERF 4 - Networking/CDN | ● High |
| COST 2 - Govern Usage | ● High |
| COST 3 - Monitor Cost | ● High |
| COST 4 - Decommission Resources | ● High |

### T — Tampering (Integrity)
| Question | Relevance |
|----------|-----------|
| SEC 6 - Compute Protection | ●● Critical |
| SEC 9 - Data in Transit | ●● Critical |
| SEC 11 - Application Security | ●● Critical |
| REL 8 - Implement Change | ●● Critical |
| OPS 5 - CI/CD Pipeline | ●● Critical |
| OPS 6 - Deployment Risks | ●● Critical |
| SEC 8 - Data at Rest | ● High |
| SEC 1 - Secure Operations | ● High |
| SEC 3 - Permissions | ● High |
| SEC 4 - Detection | ● High |
| SEC 5 - Network Protection | ● High |
| SEC 10 - Incident Response | ● High |
| REL 4 - Prevent Failures | ● High |
| REL 5 - Withstand Failures | ● High |
| REL 9 - Back Up Data | ● High |
| REL 10 - Fault Isolation | ● High |
| REL 11 - Component Failures | ● High |
| REL 12 - Test Reliability | ● High |
| REL 13 - Disaster Recovery | ● High |
| OPS 2 - Org Structure | ● High |
| OPS 7 - Readiness | ● High |
| OPS 10 - Operations Events | ● High |
| OPS 11 - Evolve Operations | ● High |
| PERF 2 - Compute Selection | ● High |
| PERF 3 - Data Management | ● High |
| PERF 4 - Networking/CDN | ● High |
| COST 2 - Govern Usage | ● High |
| SUS 6 - Keep Software Updated | ● High |

### R — Repudiation (Non-repudiability)
| Question | Relevance |
|----------|-----------|
| SEC 4 - Detection | ●● Critical |
| OPS 4 - Observability | ●● Critical |
| OPS 8 - Workload Observability | ●● Critical |
| SEC 1 - Secure Operations | ● High |
| SEC 2 - Identity Management | ● High |
| SEC 10 - Incident Response | ● High |
| REL 6 - Monitor Resources | ● High |
| REL 8 - Implement Change | ● High |
| OPS 2 - Org Structure | ● High |
| OPS 3 - Culture | ● High |
| OPS 5 - CI/CD Pipeline | ● High |
| OPS 6 - Deployment Risks | ● High |
| OPS 7 - Readiness | ● High |
| OPS 10 - Operations Events | ● High |
| PERF 3 - Data Management | ● High |
| COST 2 - Govern Usage | ● High |
| COST 3 - Monitor Cost | ● High |

### I — Information Disclosure (Confidentiality)
| Question | Relevance |
|----------|-----------|
| SEC 5 - Network Protection | ●● Critical |
| SEC 7 - Data Classification | ●● Critical |
| SEC 8 - Data at Rest | ●● Critical |
| SEC 9 - Data in Transit | ●● Critical |
| COST 4 - Decommission Resources | ●● Critical |
| SUS 4 - Data Management Policies | ●● Critical |
| SEC 6 - Compute Protection | ● High |
| SEC 3 - Permissions | ● High |
| SEC 4 - Detection | ● High |
| SEC 10 - Incident Response | ● High |
| SEC 11 - Application Security | ● High |
| REL 2 - Network Topology | ● High |
| REL 3 - Service Architecture | ● High |
| REL 9 - Back Up Data | ● High |
| REL 10 - Fault Isolation | ● High |
| REL 13 - Disaster Recovery | ● High |
| OPS 5 - CI/CD Pipeline | ● High |
| OPS 7 - Readiness | ● High |
| PERF 2 - Compute (Nitro/Enclaves) | ● High |
| PERF 3 - Data Management | ● High |
| PERF 4 - Networking/CDN | ● High |
| COST 8 - Data Transfer | ● High |
| SUS 1 - Region Selection | ● High |

### D — Denial of Service (Availability)
| Question | Relevance |
|----------|-----------|
| SEC 5 - Network Protection | ●● Critical |
| REL 1 - Service Quotas | ●● Critical |
| REL 2 - Network Topology | ●● Critical |
| REL 3 - Service Architecture | ●● Critical |
| REL 4 - Prevent Failures | ●● Critical |
| REL 5 - Withstand Failures | ●● Critical |
| REL 6 - Monitor Resources | ●● Critical |
| REL 7 - Adapt to Demand | ●● Critical |
| REL 9 - Back Up Data | ●● Critical |
| REL 10 - Fault Isolation | ●● Critical |
| REL 11 - Withstand Component Failures | ●● Critical |
| REL 12 - Test Reliability | ●● Critical |
| REL 13 - Disaster Recovery | ●● Critical |
| OPS 10 - Operations Events | ●● Critical |
| PERF 4 - Networking/CDN | ●● Critical |
| SEC 4 - Detection | ● High |
| SEC 6 - Compute Protection | ● High |
| SEC 10 - Incident Response | ● High |
| REL 8 - Implement Change | ● High |
| OPS 4 - Observability | ● High |
| OPS 6 - Deployment Risks | ● High |
| OPS 7 - Readiness | ● High |
| OPS 8 - Workload Observability | ● High |
| OPS 9 - Operations Health | ● High |
| PERF 1 - Architecture Selection | ● High |
| PERF 2 - Compute Selection | ● High |
| PERF 3 - Data Management | ● High |
| PERF 5 - Performance Testing | ● High |
| COST 2 - Govern Usage | ● High |
| COST 3 - Monitor Cost | ● High |
| COST 6 - Resource Sizing | ● High |
| COST 9 - Manage Demand | ● High |
| SUS 2 - Align to Demand | ● High |
| SUS 3 - Architecture Patterns | ● High |
| SUS 6 - Keep Software Updated | ● High |

### E — Elevation of Privilege (Authorization)
| Question | Relevance |
|----------|-----------|
| SEC 2 - Identity Management | ●● Critical |
| SEC 3 - Permissions | ●● Critical |
| SEC 6 - Compute Protection | ●● Critical |
| SEC 11 - Application Security | ●● Critical |
| REL 3 - Service Architecture | ●● Critical |
| COST 2 - Govern Usage | ●● Critical |
| COST 3 - Monitor Cost | ●● Critical |
| SEC 1 - Secure Operations | ● High |
| SEC 4 - Detection | ● High |
| SEC 5 - Network Protection | ● High |
| SEC 10 - Incident Response | ● High |
| REL 8 - Implement Change | ● High |
| REL 10 - Fault Isolation | ● High |
| OPS 2 - Org Structure | ● High |
| OPS 4 - Observability | ● High |
| OPS 5 - CI/CD Pipeline | ● High |
| OPS 6 - Deployment Risks | ● High |
| OPS 7 - Readiness | ● High |
| OPS 8 - Workload Observability | ● High |
| COST 4 - Decommission Resources | ● High |
| SUS 6 - Keep Software Updated | ● High |

---

## Pillar Contribution to STRIDE

| Pillar | S | T | R | I | D | E | Primary STRIDE Value |
|--------|---|---|---|---|---|---|---------------------|
| **Security** | ●●● | ●●● | ●●● | ●●● | ●● | ●●● | All categories — primary defense |
| **Reliability** | ● | ●● | ● | ●● | ●●● | ●● | **D** — availability is core + strong T, I, E via architecture boundaries |
| **Operational Excellence** | ●● | ●●● | ●●● | ● | ●● | ●● | **R** — observability/audit + **T** — deployment/pipeline integrity |
| **Performance Efficiency** | ● | ●● | ● | ●● | ●●● | ○ | **D** — performance = availability + **I/T** via resource selection |
| **Cost Optimization** | ● | ● | ● | ●● | ● | ●●● | **E** — governance controls + **I** — decommission/data lifecycle |
| **Sustainability** | ○ | ●● | ○ | ●● | ● | ● | **T/E** — patching + **I** — data lifecycle/minimization |

**Legend:** ●●● Extensive | ●● Strong | ● Moderate | ○ Minimal

---

## Key Insights and Recommendations

### 1. Denial of Service is a Multi-Pillar Concern
The Security pillar alone (SEC 5) provides thin DoS coverage. The **Reliability pillar dominates DoS defense** with 11 of 13 questions having Critical relevance, PERF 4 adds CDN/edge defense, and COST/SUS contribute through demand management. Always combine Security + Reliability + Performance for DoS threat modeling.

### 2. Repudiation Spans Security + Operations + Cost
While SEC 4 is the primary defense, **Operational Excellence (OPS 4, OPS 8)** provides equally critical non-repudiation through observability. Cost monitoring (COST 3) provides an independent audit signal. Your accountability strategy must span all three.

### 3. Tampering Defense Requires Security + Ops + Reliability Collaboration
Code/deployment integrity (OPS 5, OPS 6, REL 8) is as important as data/compute integrity (SEC 6, SEC 8, SEC 9). Reliability patterns (idempotency in REL 4, consistency in REL 5, recovery in REL 9/11) are also integrity controls. Treat your CI/CD pipeline AND your distributed system interactions as first-class tampering attack surfaces.

### 4. Cost Optimization is a Security Pillar in Disguise
COST 2 (govern usage) is **Critical for Elevation of Privilege** — SCPs and governance controls ARE authorization controls. COST 3 (monitor cost) is your best leading indicator of credential compromise. COST 4 (decommission) is Critical for Information Disclosure. Cost optimization is not "just finance."

### 5. Sustainability = Attack Surface Reduction + Patching
SUS 3 (remove unused components) and SUS 4 (data lifecycle) directly reduce attack surface. SUS 6 (keep software updated) is a High-relevance defense against Tampering and Elevation of Privilege. Sustainability goals and security goals are aligned — less stuff = less to protect.

### 6. Architecture = Security (Stronger Than Initially Mapped)
REL 3 (service architecture) is now **Critical for both D and E** — service boundaries are simultaneously fault boundaries, data boundaries, and trust boundaries. REL 10 (fault isolation) is High for T, I, and E. Service-oriented architecture is not just a reliability pattern — it's a foundational security control.

### 7. Organizational Controls Matter More Than Expected
OPS 2 (org structure) maps to High for T, R, and E through separation of duties. OPS 3 (culture) maps to High for R through blame-free incident reporting. OPS 7 (readiness) is High across ALL six STRIDE categories as the final gate before production. Don't dismiss "soft" organizational questions — they're the human layer of defense-in-depth.

### 8. Performance Efficiency has Hidden Security Value
PERF 2 (compute) maps to High for T and I through hardware security features (Nitro, enclaves). PERF 3 (data management) maps to High for T, R, and I through database integrity/encryption/audit features. PERF 4 (networking) maps to High for S, T, and I through CDN/edge security. Performance choices are security choices.

### 9. The "Fail-Secure" Pattern Across Pillars
REL 5 (withstand failures) and COST 6 (resource sizing) share a hidden connection: systems under stress must not relax security controls. If your rate limiter fails open, your auth bypasses under load, or your WAF disables during traffic spikes — you have a STRIDE vulnerability in your reliability/performance design.


---

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

---

# AWS Services Cross-Reference by STRIDE Category

## S — Spoofing: AWS Services for Authenticity

| Service | Defense Mechanism | Relevant WAR Questions |
|---------|-------------------|----------------------|
| **AWS IAM** | Identity federation, roles, temporary credentials, MFA enforcement | SEC 2, SEC 3 |
| **AWS IAM Identity Center (SSO)** | Centralized human identity, federated login, short-lived sessions | SEC 2 |
| **Amazon Cognito** | Application-level user auth, OAuth/OIDC, MFA | SEC 2, SEC 11 |
| **AWS Certificate Manager (ACM)** | TLS certificate management, mutual TLS for API Gateway | SEC 9, PERF 4 |
| **AWS Private CA** | Internal PKI for mTLS service-to-service authentication | REL 3, SEC 9 |
| **Amazon Route 53** | DNSSEC for DNS spoofing prevention | SEC 5, REL 2 |
| **AWS App Mesh / Service Mesh** | Service-to-service mTLS, identity-based routing | REL 3 |
| **Amazon API Gateway** | API keys, IAM auth, Cognito authorizers, Lambda authorizers | SEC 2, PERF 4 |
| **AWS STS** | Temporary security credentials, assume-role with external ID | SEC 2 |
| **Amazon GuardDuty** | Credential compromise detection, impossible travel alerts | OPS 4, SEC 4 |
| **AWS CloudTrail** | API call attribution to specific identity | SEC 4, OPS 4 |
| **AWS CodePipeline / CodeBuild** | Pipeline identity, build agent verification | OPS 5 |
| **Sigv4 / SigV4A** | Request signing prevents replay and impersonation of API calls | SEC 9 |

## T — Tampering: AWS Services for Integrity

| Service | Defense Mechanism | Relevant WAR Questions |
|---------|-------------------|----------------------|
| **AWS Config** | Configuration drift detection and auto-remediation | REL 6, SEC 6 |
| **Amazon S3 Object Lock** | Write-once-read-many (WORM) for immutable data | SEC 8, REL 9 |
| **Amazon S3 Versioning** | Version history prevents silent data overwrite | SEC 8 |
| **AWS CloudFormation / CDK** | Infrastructure as code — immutable, repeatable deployments | SEC 6, OPS 5 |
| **Amazon ECR Image Signing** | Container image integrity verification (Notation, cosign) | OPS 5, SEC 6 |
| **AWS Signer** | Code signing for Lambda, IoT, and artifacts | OPS 5, SEC 11 |
| **AWS CodeArtifact** | Trusted package repository with upstream controls | OPS 5, SEC 11 |
| **Amazon DynamoDB** | Point-in-time recovery, conditional writes for integrity | PERF 3, REL 9 |
| **Amazon RDS** | Automated backups, Multi-AZ with synchronous replication | PERF 3, REL 9, REL 11 |
| **AWS Backup** | Cross-account, cross-region immutable backups | REL 9, REL 13 |
| **AWS Systems Manager** | Patch Manager for automated patching, State Manager for desired state | SEC 6, SUS 6 |
| **Amazon Inspector** | Vulnerability scanning for compute (EC2, containers, Lambda) | SEC 6, SEC 11 |
| **AWS WAF** | Input validation rules (SQL injection, XSS prevention) | SEC 11, SEC 5 |
| **Amazon EBS Snapshots** | Point-in-time copies for recovery from corruption | REL 9 |
| **AWS Nitro System** | Hardware-level tamper resistance for compute | PERF 2, SEC 6 |
| **AWS CloudTrail Integrity Validation** | Log file digest for tamper detection | SEC 4 |
| **AWS KMS** | Envelope encryption with key rotation; HMAC for integrity verification | SEC 8, SEC 9 |

## R — Repudiation: AWS Services for Non-repudiability

| Service | Defense Mechanism | Relevant WAR Questions |
|---------|-------------------|----------------------|
| **AWS CloudTrail** | Immutable API audit trail with identity, timestamp, source IP | SEC 4, OPS 4 |
| **Amazon CloudWatch Logs** | Application and system log aggregation with retention policies | OPS 4, OPS 8 |
| **AWS X-Ray** | Distributed tracing with request attribution across services | OPS 4, OPS 8 |
| **Amazon OpenSearch (OTEL)** | OpenTelemetry-based observability with correlation | OPS 4 |
| **Amazon S3 Access Logs** | Object-level access audit trail | SEC 4, PERF 3 |
| **Amazon S3 Object Lock** | Legal hold and governance mode for evidence preservation | SEC 10, REL 13 |
| **VPC Flow Logs** | Network-level traffic attribution (source, dest, ports, bytes) | SEC 4, SEC 5 |
| **AWS CloudTrail Lake** | Queryable event data store for forensic investigation | SEC 10 |
| **Amazon EventBridge** | Event-driven audit trail for state changes | OPS 10 |
| **DynamoDB Streams** | Item-level change data capture with attribution | PERF 3 |
| **Amazon RDS Audit Logging** | Database query audit with user attribution | PERF 3 |
| **AWS Cost Explorer / CUR** | Independent spending evidence per identity/account | COST 3 |
| **AWS Organizations (CloudTrail org trail)** | Cross-account unified audit | COST 2 |
| **AWS Fault Injection Simulator** | Auditable chaos experiments with start/stop attribution | REL 12 |

## I — Information Disclosure: AWS Services for Confidentiality

| Service | Defense Mechanism | Relevant WAR Questions |
|---------|-------------------|----------------------|
| **AWS KMS** | Envelope encryption, CMK policies, key rotation | SEC 8, SEC 9 |
| **AWS CloudHSM** | Hardware-backed key storage for regulatory requirements | SEC 8 |
| **Amazon S3 Block Public Access** | Account/bucket-level public access prevention | SEC 5, SEC 8 |
| **Amazon Macie** | Automated sensitive data discovery (PII, credentials) in S3 | SEC 7, SEC 4 |
| **AWS Secrets Manager** | Secrets rotation and access control (DB creds, API keys) | SEC 8, OPS 5 |
| **Amazon VPC** | Network isolation, private subnets, VPC endpoints | SEC 5, REL 2 |
| **AWS PrivateLink** | Private connectivity without internet exposure | COST 8, REL 2 |
| **Amazon CloudFront (HTTPS)** | TLS termination at edge, origin shield | SEC 9, PERF 4 |
| **AWS Nitro Enclaves** | Isolated compute for sensitive data processing (no operator access) | PERF 2 |
| **Amazon RDS Encryption** | Transparent data encryption at storage layer | SEC 8, PERF 3 |
| **AWS Lake Formation** | Column-level, row-level, and cell-level data access controls | SEC 3, SEC 7 |
| **Amazon DynamoDB Encryption Client** | Client-side encryption for field-level protection | SEC 8 |
| **S3 Lifecycle Policies** | Automated data deletion (reducing breach window) | SUS 4, COST 4 |
| **AWS Backup Vault Lock** | Encrypted backup with access restrictions | REL 9 |
| **Amazon GuardDuty** | Data exfiltration detection (DNS, S3) | SEC 4 |
| **VPC Endpoints (Gateway/Interface)** | Keep traffic off public internet | SEC 5, COST 8 |
| **AWS RAM (Resource Access Manager)** | Controlled cross-account resource sharing | REL 3, SEC 3 |
| **Security Groups / NACLs** | Micro-segmentation limiting data flow | SEC 5 |
| **AWS Certificate Manager** | Automated TLS cert provisioning and renewal | SEC 9 |

## D — Denial of Service: AWS Services for Availability

| Service | Defense Mechanism | Relevant WAR Questions |
|---------|-------------------|----------------------|
| **AWS Shield (Standard & Advanced)** | Managed DDoS protection (L3/L4/L7) | SEC 5 |
| **AWS WAF** | Rate limiting, geo-blocking, bot control, custom rules | SEC 5, PERF 4 |
| **Amazon CloudFront** | Edge caching absorbs volumetric attacks at perimeter | PERF 4 |
| **AWS Global Accelerator** | Anycast routing, automatic failover across regions | PERF 4, REL 13 |
| **Elastic Load Balancing (ALB/NLB)** | Traffic distribution, health checks, multi-AZ | REL 11 |
| **Auto Scaling (EC2, ECS, Lambda)** | Absorb traffic spikes by scaling horizontally | REL 7 |
| **Amazon SQS** | Queue-based load leveling decouples producers from consumers | COST 9, REL 4 |
| **Amazon API Gateway** | Throttling, usage plans, burst limits per API key | COST 9, REL 4 |
| **AWS Service Quotas** | Proactive quota monitoring and request increase | REL 1 |
| **Amazon Route 53** | Health checks, DNS failover, latency-based routing | REL 2, REL 11 |
| **Multi-AZ Deployments (RDS, ElastiCache)** | Automatic failover for database tier | REL 11 |
| **AWS Backup / Cross-Region Replication** | Recovery from destructive attacks (region failure, wiper) | REL 9, REL 13 |
| **AWS Fault Injection Simulator** | Chaos engineering to validate resilience | REL 12 |
| **Amazon CloudWatch Alarms** | Proactive detection of resource exhaustion | REL 6, OPS 8 |
| **AWS Budgets** | Detect resource abuse via cost anomaly | COST 3 |
| **Circuit Breaker patterns (App Mesh, custom)** | Prevent cascading failures across services | REL 4, REL 5 |
| **Cell-based architecture (shuffle sharding)** | Limit blast radius of any single failure | REL 10 |
| **Canary/Rolling deployments (CodeDeploy)** | Prevent deployment-caused outages | OPS 6 |
| **AWS Systems Manager Patch Manager** | Patch known DoS vulnerabilities | SUS 6 |

## E — Elevation of Privilege: AWS Services for Authorization

| Service | Defense Mechanism | Relevant WAR Questions |
|---------|-------------------|----------------------|
| **AWS IAM Policies** | Least privilege, resource-based policies, condition keys | SEC 3 |
| **IAM Permission Boundaries** | Hard ceiling on maximum permissions regardless of policy | SEC 3, COST 2 |
| **AWS Organizations SCPs** | Account-level guardrails that override IAM policies | COST 2 |
| **AWS Control Tower** | Preventive and detective guardrails across accounts | COST 2, OPS 2 |
| **IAM Access Analyzer** | Identifies overly permissive policies and cross-account access | SEC 3 |
| **Amazon VPC Security Groups** | Micro-segmentation prevents lateral movement | SEC 5, REL 10 |
| **AWS Systems Manager Session Manager** | Audited, controlled access without SSH/RDP (no open ports) | SEC 6 |
| **Amazon ECS/EKS Task Roles** | Per-container/pod IAM roles (no shared node credentials) | SEC 6, REL 3 |
| **AWS Lambda Execution Roles** | Function-level least privilege | SEC 3, REL 3 |
| **Amazon Cognito Groups** | Application-level RBAC for end users | SEC 2, SEC 11 |
| **AWS WAF** | Block common privilege escalation payloads (injection, path traversal) | SEC 11 |
| **Amazon Inspector** | Detect privilege escalation vulnerabilities in OS/containers | SEC 6, SUS 6 |
| **AWS Config Rules** | Detect permission creep, non-compliant IAM configurations | SEC 3, COST 2 |
| **GuardDuty** | Detect unusual API calls indicating privilege abuse | SEC 4, OPS 4 |
| **CloudTrail + Athena** | Query for privilege escalation patterns (CreateRole, AttachPolicy, AssumeRole) | SEC 4, COST 3 |
| **AWS Verified Access** | Zero-trust network access without VPN (identity-based, not network-based) | SEC 5, REL 2 |
| **CodePipeline Approval Actions** | Require human approval for production deployments | OPS 6, REL 8 |
| **AWS Audit Manager** | Continuous compliance evidence for access controls | OPS 7 |
| **Resource-based policies (S3, KMS, SQS)** | Cross-account access with explicit principal specification | SEC 3, REL 3 |

---

# Quick Reference: Top 5 AWS Services per STRIDE Category

For teams starting their threat model and needing to prioritize service adoption:

| STRIDE | #1 | #2 | #3 | #4 | #5 |
|--------|-----|-----|-----|-----|-----|
| **S** Spoofing | IAM Identity Center | Cognito | Private CA (mTLS) | GuardDuty | API Gateway Authorizers |
| **T** Tampering | AWS Config | S3 Object Lock | CodeArtifact + Signer | AWS Backup | KMS (HMAC) |
| **R** Repudiation | CloudTrail | X-Ray | CloudWatch Logs | S3 Access Logs | VPC Flow Logs |
| **I** Disclosure | KMS | VPC + PrivateLink | Macie | S3 Block Public Access | Secrets Manager |
| **D** DoS | Shield Advanced | WAF | CloudFront | Auto Scaling | SQS (load leveling) |
| **E** Privilege | SCPs | Permission Boundaries | IAM Access Analyzer | Security Groups | GuardDuty |

---

# Threat Model Workshop Template

Use this during architecture reviews. For each system component, walk through:

```
┌─────────────────────────────────────────────────────────────┐
│ Component: _______________________________________________  │
│ WAR Workload: ____________________________________________  │
├─────────────────────────────────────────────────────────────┤
│ S - Who/what can impersonate this component?                │
│     Controls in place: ___________________________________  │
│     WAR gaps (SEC 2, REL 3, OPS 5): _____________________  │
│     AWS services: ________________________________________  │
├─────────────────────────────────────────────────────────────┤
│ T - Who/what can modify this component's data or code?      │
│     Controls in place: ___________________________________  │
│     WAR gaps (SEC 6, OPS 5/6, REL 8): ___________________  │
│     AWS services: ________________________________________  │
├─────────────────────────────────────────────────────────────┤
│ R - Can actions on this component be denied?                │
│     Controls in place: ___________________________________  │
│     WAR gaps (SEC 4, OPS 4/8): ___________________________  │
│     AWS services: ________________________________________  │
├─────────────────────────────────────────────────────────────┤
│ I - What sensitive data can this component expose?           │
│     Controls in place: ___________________________________  │
│     WAR gaps (SEC 7/8/9, REL 10, COST 4): _______________  │
│     AWS services: ________________________________________  │
├─────────────────────────────────────────────────────────────┤
│ D - How can this component be made unavailable?             │
│     Controls in place: ___________________________________  │
│     WAR gaps (SEC 5, REL 1-13, PERF 4): _________________  │
│     AWS services: ________________________________________  │
├─────────────────────────────────────────────────────────────┤
│ E - How can privileges be escalated through this component? │
│     Controls in place: ___________________________________  │
│     WAR gaps (SEC 3, REL 3, COST 2): ____________________  │
│     AWS services: ________________________________________  │
└─────────────────────────────────────────────────────────────┘
```


---

# Reverse Mapping: STRIDE → WAFR

Use this section when you start from a threat model (STRIDE analysis) and need to identify which Well-Architected questions to answer, which best practices to implement, and which AWS services to deploy.

## S — Spoofing: "Who or what can pretend to be something it's not?"

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

---

## T — Tampering: "Who or what can modify data, code, or configuration without authorization?"

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

---

## R — Repudiation: "Can someone deny having performed an action?"

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

---

## I — Information Disclosure: "What sensitive data can be exposed to unauthorized parties?"

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

---

## D — Denial of Service: "How can this system be made unavailable?"

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

---

## E — Elevation of Privilege: "How can someone gain access they shouldn't have?"

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

---

## Reverse Lookup Quick Reference

For rapid navigation — "I found this threat, which WAR questions do I check?"

| If you found this threat... | Check these WAR questions first | Then these |
|-----------------------------|-------------------------------|------------|
| Stolen API keys / credentials | SEC 2, SEC 3, OPS 4, COST 3 | SEC 4, OPS 8, COST 4 |
| Man-in-the-middle | SEC 9, SEC 5, REL 2, PERF 4 | SEC 4 |
| SQL injection / RCE | SEC 11, SEC 6, SEC 5 | OPS 5, SEC 4 |
| Ransomware / data wipe | REL 9, REL 13, SEC 8, SEC 6 | OPS 10, SEC 10 |
| DDoS attack | SEC 5, PERF 4, REL 7, COST 9 | REL 4, REL 5, REL 10 |
| Insider threat (admin gone rogue) | SEC 3, OPS 2, SEC 4, COST 2 | OPS 4, REL 8 |
| Supply chain compromise (bad package) | OPS 5, SEC 11, SEC 6 | OPS 6 |
| Data breach / exfiltration | SEC 7, SEC 8, SEC 5, SEC 4 | COST 4, SUS 4, REL 10 |
| Accidental public exposure (open S3) | SEC 8, SEC 5, COST 4, SEC 3 | SEC 7, OPS 7 |
| Cascading failure (one service takes all down) | REL 4, REL 5, REL 10, REL 3 | REL 6, OPS 10 |
| Deployment breaks production | OPS 6, REL 8, REL 12 | OPS 7, OPS 10 |
| Crypto-mining on compromised instance | COST 3, SEC 6, SEC 2, SEC 4 | COST 2, OPS 4 |
| Privilege escalation via IAM misconfiguration | SEC 3, COST 2, SEC 4 | OPS 4, COST 3 |
| Log deletion / evidence tampering | SEC 4, SEC 8, OPS 4 | OPS 8, REL 13 |
| Service impersonation | REL 3, SEC 9, SEC 2 | OPS 5, PERF 4 |
| Region-level data sovereignty violation | SUS 1, SEC 8, REL 13 | SEC 7 |
| Orphaned infrastructure attack | COST 4, SEC 3, COST 2 | SUS 3, SEC 4 |
| Failed failover exposes data | REL 5, REL 13, SEC 8, REL 10 | SEC 9 |
