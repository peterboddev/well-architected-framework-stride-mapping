# JIRA Task Output Format

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

---

## Example JIRA Tasks (What Kiro Generates)

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
