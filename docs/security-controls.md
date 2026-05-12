# Security Controls

## Purpose

This document maps the security controls used in the secure multi-account AWS retail architecture. Controls are grouped into preventive, detective, responsive, and governance categories.

The goal is to show how the architecture reduces risk through layered controls rather than relying on any single AWS service.

---

## Control Summary

| Control category | Security objective |
|---|---|
| Preventive controls | Reduce the likelihood of unauthorized access, overexposure, and insecure configuration |
| Detective controls | Improve visibility into suspicious activity, misconfiguration, and policy drift |
| Responsive controls | Support alerting, investigation, containment, and remediation |
| Governance controls | Standardize security expectations across accounts and environments |

---

## Preventive Controls

| Control | AWS service or design choice | Purpose |
|---|---|---|
| Multi-account separation | AWS Organizations | Separates management, security, logging, shared services, production, and non-production trust boundaries |
| Production/non-production isolation | Separate AWS accounts and VPCs | Prevents development or test access paths from affecting production workloads |
| Service control policies | AWS Organizations SCPs | Restricts high-risk actions across accounts, such as disabling logging or making storage public |
| Least-privilege IAM | IAM roles, permission boundaries, IAM Identity Center | Limits administrative and workload access to approved actions |
| Controlled human access | IAM Identity Center | Reduces reliance on long-lived credentials and improves auditability of administrative access |
| Layered public ingress | Route 53, CloudFront, AWS WAF, ALB | Ensures public traffic enters through controlled inspection and routing layers |
| Private workload placement | Private subnets, security groups, route tables | Keeps application and database tiers away from direct internet exposure |
| Network segmentation | VPCs, public/private subnets, security groups | Restricts communication paths between edge, application, and database tiers |
| Encryption at rest | KMS, RDS encryption, S3 encryption, DynamoDB encryption where used | Protects sensitive data stored by application and platform services |
| Secrets protection | AWS Secrets Manager, KMS | Reduces exposure of database credentials, API secrets, and operational secrets |
| S3 public access prevention | S3 Block Public Access, bucket policies | Reduces risk of accidental public exposure of logs or application data |

---

## Detective Controls

| Control | AWS service or design choice | Purpose |
|---|---|---|
| Organization activity logging | AWS CloudTrail | Captures API activity across accounts for audit and investigation |
| Centralized log archive | Dedicated log archive account and S3 | Preserves security logs outside workload accounts |
| Threat detection | Amazon GuardDuty | Detects suspicious account, network, and credential activity |
| Security posture aggregation | AWS Security Hub | Aggregates security findings and posture signals across accounts |
| Configuration monitoring | AWS Config | Detects configuration drift and noncompliant resource states |
| Network visibility | VPC Flow Logs | Supports investigation of network traffic patterns and suspicious connections |
| Operational monitoring | Amazon CloudWatch | Provides metrics, logs, and alarms for workload health and security-relevant events |
| Edge and application logging | CloudFront, AWS WAF, ALB access logs | Supports review of public traffic, blocked requests, and abnormal access patterns |

---

## Responsive Controls

| Control | AWS service or design choice | Purpose |
|---|---|---|
| Security alert routing | EventBridge, SNS | Sends high-priority findings or events to security responders |
| Automated response patterns | Lambda, SSM Automation | Supports remediation of known issues such as public storage or risky security group changes |
| Account containment | SCPs, IAM policy updates, security group updates | Restricts compromised identities or workloads during investigation |
| Finding triage | Security Hub, GuardDuty, CloudWatch | Supports prioritization of alerts and investigation workflows |
| Forensic evidence preservation | Log archive account, CloudTrail, VPC Flow Logs | Maintains records needed for incident investigation |
| Recovery support | RDS Multi-AZ, Auto Scaling Groups, backups | Supports restoration and continuity after failure or compromise |

---

## Governance Controls

| Control | AWS service or design choice | Purpose |
|---|---|---|
| Organizational unit structure | AWS Organizations | Groups accounts by function and risk profile |
| Account baselines | Control Tower-style guardrails or standardized account vending | Ensures accounts start with expected logging, monitoring, and access controls |
| Centralized security ownership | Security tooling account | Separates security visibility from workload administration |
| Log retention and evidence storage | Log archive account, S3 retention controls | Supports audit readiness and investigation |
| Configuration compliance | AWS Config rules, Security Hub standards | Provides continuous visibility into control drift |
| Access governance | IAM Identity Center, access reviews, permission boundaries | Supports periodic review of human and workload access |
| Framework alignment | CIS AWS Foundations, NIST CSF, SOC 2, PCI DSS concepts | Connects architecture decisions to security, compliance, and audit-readiness objectives |

---

## Retail-Specific Control Considerations

| Retail concern | Security control response |
|---|---|
| Customer account abuse | Strong identity controls, logging, GuardDuty, WAF, and anomaly visibility |
| Order workflow disruption | Multi-AZ architecture, Auto Scaling, controlled ingress, monitoring |
| Payment-adjacent service exposure | Account segmentation, encryption, private subnets, least-privilege access |
| Seasonal traffic spikes | CloudFront, ALB, Auto Scaling, monitoring and resilience planning |
| Audit and compliance expectations | Centralized logs, Config, Security Hub, evidence retention, framework mapping |
| Store or internal system integration | Shared services account, controlled network paths, IAM boundaries |

---

## Key Takeaway

The architecture uses layered controls to reduce risk across identity, network, workload, data, monitoring, response, and governance domains. The value is not in any single AWS service, but in how the controls work together across accounts and trust boundaries.
