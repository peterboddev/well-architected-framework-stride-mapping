# Well-Architected Framework ↔ STRIDE Threat Model Mapping

A bidirectional mapping between the AWS Well-Architected Framework (58 questions, 6 pillars) and the STRIDE threat modeling framework, designed to be used with Kiro for automated code review and JIRA task generation.

## Quick Start

1. **Open this workspace in Kiro** alongside your application/infrastructure code repository
2. **Ask Kiro** to review your code against this mapping:
   - `"Review my code against the STRIDE mapping for the Security pillar"`
   - `"What WAR questions does my codebase fail to address?"`
   - `"Generate JIRA tasks for gaps in my Denial of Service defenses"`
3. **Export findings** as JIRA tasks using the template in `tools/jira-task-template.md`

## Directory Structure

| File | Description |
|------|-------------|
| [README.md](README.md) | You are here |
| [war-stride-mapping.md](war-stride-mapping.md) | Single-file version (portable, self-contained) |
| [stride-reference.md](stride-reference.md) | STRIDE categories + Top 5 AWS services quick ref |

### Forward Mapping — WAFR → STRIDE

Start here if you're reviewing Well-Architected answers and want to know which threats they address.

| File | Covers |
|------|--------|
| [security-pillar.md](forward-mapping/security-pillar.md) | SEC 1–11 |
| [reliability-pillar.md](forward-mapping/reliability-pillar.md) | REL 1–13 |
| [operational-excellence-pillar.md](forward-mapping/operational-excellence-pillar.md) | OPS 1–11 |
| [performance-efficiency-pillar.md](forward-mapping/performance-efficiency-pillar.md) | PERF 1–5 |
| [cost-optimization-pillar.md](forward-mapping/cost-optimization-pillar.md) | COST 1–11 |
| [sustainability-pillar.md](forward-mapping/sustainability-pillar.md) | SUS 1–6 |

### Reverse Mapping — STRIDE → WAFR

Start here if you're threat modeling and want to know which WAR questions to address for each threat.

| File | Threat Category |
|------|-----------------|
| [spoofing.md](reverse-mapping/spoofing.md) | Attack vectors, WAFR questions, defense strategy |
| [tampering.md](reverse-mapping/tampering.md) | Attack vectors, WAFR questions, defense strategy |
| [repudiation.md](reverse-mapping/repudiation.md) | Attack vectors, WAFR questions, defense strategy |
| [information-disclosure.md](reverse-mapping/information-disclosure.md) | Attack vectors, WAFR questions, defense strategy |
| [denial-of-service.md](reverse-mapping/denial-of-service.md) | Attack vectors, WAFR questions, defense strategy |
| [elevation-of-privilege.md](reverse-mapping/elevation-of-privilege.md) | Attack vectors, WAFR questions, defense strategy |

### Analysis

| File | Contents |
|------|----------|
| [cross-pillar-summary.md](analysis/cross-pillar-summary.md) | Summary matrix + pillar contributions to STRIDE |
| [key-insights.md](analysis/key-insights.md) | 9 actionable insights from the mapping |

### Tools

| File | Purpose |
|------|---------|
| [checklists.md](tools/checklists.md) | 94 check items across all 6 STRIDE categories |
| [aws-services-reference.md](tools/aws-services-reference.md) | AWS services per STRIDE category |
| [jira-task-template.md](tools/jira-task-template.md) | Template + 3 worked examples |
| [workshop-template.md](tools/workshop-template.md) | Per-component threat modeling template |

## How to Use

### Entry Points (Choose Based on Your Goal)

| Your Goal | Start Here | Then |
|-----------|-----------|------|
| "I'm answering WAR questions, what threats do they map to?" | `forward-mapping/<pillar>.md` | Check gaps in `tools/checklists.md` |
| "I found a threat in my architecture, which WAR questions help?" | `reverse-mapping/<category>.md` | Implement defense strategy |
| "I want Kiro to review my code automatically" | This README → "Kiro Commands" below | Review generated JIRA tasks |
| "I need to generate JIRA tickets for my team" | `tools/jira-task-template.md` | Apply labels and priority from template |
| "I want to run a threat modeling workshop" | `tools/workshop-template.md` | Use `stride-reference.md` as handout |
| "I have a Confluence WAR export to reconcile" | This README → "Reconciliation Modes" | Compare against code evidence |

### Kiro Commands

Ask Kiro these prompts to trigger different review modes:

```
# Full review (all pillars, all STRIDE categories)
"Review my code against the complete WAR-STRIDE mapping and generate JIRA tasks"

# Pillar-specific review
"Check my infrastructure code against the Reliability pillar mapping"
"Review my IAM policies against SEC 2 and SEC 3"

# STRIDE-specific review
"What Elevation of Privilege threats exist in my codebase?"
"Check my code for Information Disclosure gaps"

# Targeted threat scenarios
"Check if my system is resilient to ransomware (use the Tampering reverse mapping)"
"Is my API vulnerable to DDoS? Review against the Denial of Service mapping"

# JIRA generation
"Generate JIRA tasks for all Critical-priority gaps you found"
"Create JIRA stories for the Spoofing checklist items that fail"

# Confluence reconciliation
"Compare the Confluence WAR export in /docs against what's implemented in my code"
```

### Reconciliation Modes

| Mode | Input | Use When |
|------|-------|----------|
| **Code-only review** | This mapping + code repo | No prior WAR review; fresh assessment |
| **Confluence reconciliation** | This mapping + Confluence export + code repo | Prior WAR review; checking implementation status |
| **Targeted STRIDE review** | This mapping + code repo + specific STRIDE category | Focused threat model for one category |
| **Pillar-specific review** | This mapping + code repo + specific pillar | Focused on one WAR pillar |

### JIRA Integration

All findings follow a consistent structure (see `tools/jira-task-template.md`):
- **Summary:** `[STRIDE-X] <concise gap description>`
- **Labels:** `war-review`, `stride-<category>`, `<pillar>-pillar`, `war-<id>`
- **Priority:** Derived from mapping relevance × implementation gap severity
- **Acceptance Criteria:** Testable conditions for "done"

### What Kiro Scans For

When pointed at your code repo, Kiro looks for:

| Code Type | What's Checked |
|-----------|---------------|
| **IaC** (CDK, Terraform, CloudFormation) | IAM policies, encryption config, network rules, backup settings, scaling policies |
| **Application code** | Auth patterns, input validation, error handling, logging, data access patterns |
| **CI/CD config** (GitHub Actions, CodePipeline, Jenkins) | Signing, approval gates, secret handling, artifact provenance |
| **Config files** | Encryption settings, retention policies, access controls, service quotas |
| **Dockerfiles / container config** | Privilege settings, base image currency, capability drops |

## Key Insights (from Analysis)

1. **Denial of Service requires Reliability pillar** — Security alone (SEC 5) is insufficient; REL 1-13 dominates DoS defense
2. **Your CI/CD pipeline is a first-class attack surface** — OPS 5, OPS 6, REL 8 address Tampering via deployment
3. **Cost monitoring IS security monitoring** — COST 3 is a top signal for credential compromise
4. **Service boundaries = trust boundaries** — REL 3 is Critical for both Availability AND Authorization
5. **Data lifecycle = breach window** — SUS 4 (delete data you don't need) directly reduces Information Disclosure risk

## Contributing

To update this mapping:
1. Edit the relevant file in `forward-mapping/` or `reverse-mapping/`
2. Update `analysis/cross-pillar-summary.md` if priorities changed
3. Update `tools/checklists.md` if new check items are needed
4. The single-file `war-stride-mapping.md` can be regenerated by concatenating all sections

## References

- [AWS Well-Architected Framework](https://docs.aws.amazon.com/wellarchitected/latest/framework/welcome.html)
- [STRIDE Threat Model (Microsoft)](https://learn.microsoft.com/en-us/azure/security/develop/threat-modeling-tool-threats)
- [AWS Well-Architected Tool API](https://docs.aws.amazon.com/cli/latest/reference/wellarchitected/index.html)
