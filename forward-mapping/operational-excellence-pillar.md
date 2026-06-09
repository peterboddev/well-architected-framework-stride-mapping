# Operational Excellence Pillar → STRIDE Mapping

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
