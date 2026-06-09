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

## Quick Reference: Top 5 AWS Services per STRIDE Category

For teams starting their threat model and needing to prioritize service adoption:

| STRIDE | #1 | #2 | #3 | #4 | #5 |
|--------|-----|-----|-----|-----|-----|
| **S** Spoofing | IAM Identity Center | Cognito | Private CA (mTLS) | GuardDuty | API Gateway Authorizers |
| **T** Tampering | AWS Config | S3 Object Lock | CodeArtifact + Signer | AWS Backup | KMS (HMAC) |
| **R** Repudiation | CloudTrail | X-Ray | CloudWatch Logs | S3 Access Logs | VPC Flow Logs |
| **I** Disclosure | KMS | VPC + PrivateLink | Macie | S3 Block Public Access | Secrets Manager |
| **D** DoS | Shield Advanced | WAF | CloudFront | Auto Scaling | SQS (load leveling) |
| **E** Privilege | SCPs | Permission Boundaries | IAM Access Analyzer | Security Groups | GuardDuty |
