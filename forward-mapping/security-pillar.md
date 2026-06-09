# Security Pillar → STRIDE Mapping

## SEC 1: How do you securely operate your workload?
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
