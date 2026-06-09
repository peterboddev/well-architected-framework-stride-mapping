# Performance Efficiency Pillar → STRIDE Mapping

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
