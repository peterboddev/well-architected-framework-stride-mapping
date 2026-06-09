# Sustainability Pillar → STRIDE Mapping

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
