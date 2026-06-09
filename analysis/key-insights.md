# Key Insights and Recommendations

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
