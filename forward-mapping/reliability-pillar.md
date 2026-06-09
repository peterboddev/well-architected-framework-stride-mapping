# Reliability Pillar → STRIDE Mapping

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
