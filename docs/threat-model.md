# Threat Model

## Purpose

This threat model documents the primary risks considered for a secure multi-account AWS retail architecture. The goal is to show how the architecture reduces risk through account isolation, centralized visibility, private workload placement, least-privilege access, and layered ingress controls.

This is not intended to model every possible retail attack path. It focuses on the most relevant threats for a customer-facing retail platform that supports customer identity, product browsing, order workflows, internal APIs, and payment-adjacent services.

---

## Business Context

The architecture represents a retail platform modernizing on AWS. The environment includes production and non-production workloads, centralized logging, security visibility, shared services, and retail application accounts.

Key business concerns include:

- protecting customer identity and order workflows
- reducing exposure of payment-adjacent systems
- preventing non-production access paths from impacting production
- preserving audit logs and security evidence
- detecting suspicious activity across accounts
- supporting secure growth without placing all workloads in one AWS account

---

## Key Assets

| Asset | Why it matters |
|---|---|
| Customer identity data | Supports authentication, account access, and customer trust |
| Order workflow services | Supports revenue-generating customer transactions |
| Payment-adjacent services | May process or interact with sensitive transaction workflows |
| Production application tier | Hosts customer-facing and internal retail application logic |
| Database tier | Stores application and operational data |
| Centralized logs | Supports detection, auditability, investigation, and incident response |
| Security tooling account | Aggregates findings and visibility across accounts |
| IAM roles and administrative access | Controls who can access and operate the environment |

---

## Trust Boundaries

| Trust boundary | Risk reduced |
|---|---|
| Production vs. non-production accounts | Reduces the chance that test/dev access paths or misconfigurations affect customer-facing production workloads |
| Workload accounts vs. security tooling account | Separates application operations from security monitoring and findings aggregation |
| Workload accounts vs. log archive account | Protects security logs from tampering by workload administrators |
| Public ingress vs. private application tier | Limits direct internet exposure of application workloads |
| Application tier vs. database tier | Restricts database access to approved application paths |
| Human access vs. workload access | Separates administrative access from application execution roles |
| Payment-adjacent workflows vs. general workloads | Reduces exposure of sensitive transaction-related services |

---

## Threat Model & Controls Matrix

| Threat | Example attack path | Potential impact | Controls and mitigations |
|---|---|---|---|
| Account takeover | Attacker uses stolen customer or administrator credentials to access retail systems | Fraud, unauthorized access, data exposure, reputational damage | MFA, IAM Identity Center, least-privilege roles, CloudTrail monitoring, GuardDuty findings |
| Lateral movement across accounts | Compromised workload role attempts to access other AWS accounts or assume broader permissions | Expanded blast radius, production compromise, unauthorized data access | AWS Organizations, separate workload accounts, SCP guardrails, IAM permission boundaries, centralized CloudTrail |
| Payment-adjacent data exposure | Sensitive transaction-related systems are overexposed or accessed through excessive permissions | Compliance impact, customer harm, financial risk | Account segmentation, encryption with KMS, private subnets, least-privilege IAM, centralized logging |
| Public API or web abuse | Bot traffic, injection attempts, credential stuffing, or abusive request patterns target the retail entry point | Service disruption, fraud, increased operational cost | CloudFront, AWS WAF, ALB, security groups, access logging, rate limiting patterns |
| Log tampering or evidence loss | Compromised workload administrator attempts to delete or alter security logs | Loss of forensic evidence, weaker auditability, delayed investigation | Centralized log archive account, organization CloudTrail, S3 bucket protections, restricted log access |
| Misconfigured storage | Public S3 bucket, overly permissive bucket policy, or weak encryption configuration exposes data | Data leakage, compliance findings, customer trust impact | S3 Block Public Access, encryption by default, AWS Config rules, Security Hub findings, SCP guardrails |
| Overprivileged IAM access | Users or workloads have broader permissions than required | Privilege escalation, accidental or malicious misuse | IAM Identity Center, least privilege roles, permission boundaries, access reviews, CloudTrail monitoring |
| Non-production to production bleed-over | Shared credentials, shared networks, or weak account separation allow dev/test access into production | Unauthorized production access, accidental production changes | Separate accounts, separate VPCs, environment-specific IAM roles, production-only access paths |
| Database exposure | Application or database tier is placed in public subnets or allows broad inbound access | Unauthorized data access, data exfiltration | Private subnets, security group restrictions, RDS Multi-AZ private placement, encryption at rest |
| Weak security visibility | Logs and findings remain fragmented across accounts | Delayed detection, inconsistent response, poor audit readiness | GuardDuty, Security Hub, AWS Config aggregation, CloudTrail, CloudWatch, VPC Flow Logs |

---

## Assumptions

This threat model assumes:

- AWS Organizations is used to separate accounts and enforce governance boundaries
- production and non-production environments are separated at the account level
- centralized logging and findings aggregation are configured in dedicated security/logging accounts
- human administrative access is governed through IAM Identity Center or equivalent federated access
- workloads are designed to run in private subnets where possible
- internet-facing access is intentionally routed through CloudFront, AWS WAF, and ALB

---

## Out of Scope

This threat model does not fully model:

- every possible retail fraud scenario
- detailed application code vulnerabilities
- payment processor internals
- full PCI DSS implementation details
- third-party vendor risk
- complete incident response runbooks

These items could be addressed in future extensions of the architecture.

---

## Key Takeaway

The main security objective is to reduce blast radius and improve visibility. The architecture does this by separating accounts, centralizing logs and findings, placing workloads privately by default, enforcing least privilege, and routing customer-facing traffic through layered ingress controls.
