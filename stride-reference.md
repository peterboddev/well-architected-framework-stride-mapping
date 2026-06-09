# STRIDE Categories Reference

## The Six Threat Categories

| Category | Threat | Security Property Violated | Question to Ask |
|----------|--------|---------------------------|-----------------|
| **S** - Spoofing | Impersonating a user or system | Authenticity | "Who or what can pretend to be something it's not?" |
| **T** - Tampering | Modifying data or code | Integrity | "Who or what can modify data/code without authorization?" |
| **R** - Repudiation | Denying an action was performed | Non-repudiability | "Can someone deny having performed an action?" |
| **I** - Information Disclosure | Exposing data to unauthorized parties | Confidentiality | "What sensitive data can be exposed?" |
| **D** - Denial of Service | Making system unavailable | Availability | "How can this system be made unavailable?" |
| **E** - Elevation of Privilege | Gaining unauthorized access levels | Authorization | "How can someone gain access they shouldn't have?" |

---

## Quick Reference: Top 5 AWS Services per STRIDE Category

| STRIDE | #1 | #2 | #3 | #4 | #5 |
|--------|-----|-----|-----|-----|-----|
| **S** Spoofing | IAM Identity Center | Cognito | Private CA (mTLS) | GuardDuty | API Gateway Authorizers |
| **T** Tampering | AWS Config | S3 Object Lock | CodeArtifact + Signer | AWS Backup | KMS (HMAC) |
| **R** Repudiation | CloudTrail | X-Ray | CloudWatch Logs | S3 Access Logs | VPC Flow Logs |
| **I** Disclosure | KMS | VPC + PrivateLink | Macie | S3 Block Public Access | Secrets Manager |
| **D** DoS | Shield Advanced | WAF | CloudFront | Auto Scaling | SQS (load leveling) |
| **E** Privilege | SCPs | Permission Boundaries | IAM Access Analyzer | Security Groups | GuardDuty |

---

## Reverse Lookup Quick Reference

"I found this threat — which WAR questions do I check?"

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
| Cascading failure | REL 4, REL 5, REL 10, REL 3 | REL 6, OPS 10 |
| Deployment breaks production | OPS 6, REL 8, REL 12 | OPS 7, OPS 10 |
| Crypto-mining on compromised instance | COST 3, SEC 6, SEC 2, SEC 4 | COST 2, OPS 4 |
| Privilege escalation via IAM | SEC 3, COST 2, SEC 4 | OPS 4, COST 3 |
| Log deletion / evidence tampering | SEC 4, SEC 8, OPS 4 | OPS 8, REL 13 |
| Service impersonation | REL 3, SEC 9, SEC 2 | OPS 5, PERF 4 |
| Data sovereignty violation | SUS 1, SEC 8, REL 13 | SEC 7 |
| Orphaned infrastructure attack | COST 4, SEC 3, COST 2 | SUS 3, SEC 4 |
| Failed failover exposes data | REL 5, REL 13, SEC 8, REL 10 | SEC 9 |
