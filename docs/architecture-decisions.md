# Architecture Decisions

## 1. Why a multi-account model?

A multi-account structure improves trust boundary separation, limits blast radius, supports clearer ownership, and enables centralized security visibility across environments.

This design uses separate accounts for:
- management and identity
- security and log archive
- shared services
- retail production
- retail non-production

This approach reflects a more mature AWS operating model than placing all workloads inside one account.

## 2. Why separate production and non-production accounts?

Production and non-production environments often have different risk profiles, access patterns, and operational controls.

Separating them at the account level reduces the chance of accidental cross-environment access, improves governance, and makes it easier to apply environment-specific controls.

## 3. Why EC2 Auto Scaling Groups instead of containers?

This architecture uses EC2 Auto Scaling Groups behind an Application Load Balancer to reflect a realistic modernization path for retail workloads that need stronger security, resilience, and operational consistency without requiring an immediate move to containers.

This choice also highlights:
- controlled ingress
- private subnet hosting
- resilient scaling across AZs
- operational familiarity for traditionally hosted workloads

## 4. Why CloudFront + WAF + ALB?

This layered ingress model reduces attack surface and provides defense in depth.

- CloudFront helps control edge delivery and reduce unnecessary direct exposure
- AWS WAF adds inspection and filtering at the edge layer
- ALB provides application-aware routing into private workloads

Together, these services support a cleaner, more controlled public entry path.

## 5. Why keep application and database tiers private?

Private placement reduces direct exposure and aligns with a private-by-default design philosophy.

In this architecture:
- the application tier runs in private subnets
- the database tier runs in private subnets
- only the edge and load-balancing path is internet-facing

This limits reachable surfaces and supports cleaner traffic boundaries.

## 6. Why centralize logging and findings?

Centralized logging and findings aggregation improve auditability, reduce fragmentation, and support faster detection and response across accounts.

This architecture centralizes:
- CloudTrail logs
- AWS Config aggregation
- GuardDuty findings
- Security Hub visibility
- long-term log archive storage

This also supports posture visibility and CSPM-style operational review across the environment.

## 7. Why use IAM Identity Center for administrative access?

IAM Identity Center provides a more governable model for human access into workload accounts.

This supports:
- controlled role assumption
- clearer permission boundaries
- better auditability
- reduced reliance on long-lived credentials

## 8. Why Multi-AZ resilience for ASG and RDS?

Security and resilience should be designed together.

This architecture places:
- EC2 Auto Scaling Groups across multiple AZs
- RDS in a Multi-AZ configuration

This improves recovery posture and supports business continuity in the face of instance or AZ-level disruption.

## 9. Why separate security tooling and log archive responsibilities?

Security tooling and log archive responsibilities are separated to reduce the risk that workload administrators can modify detection configuration or tamper with audit evidence.

The security tooling account is responsible for findings aggregation and posture visibility. The log archive account preserves activity logs and evidence for investigation and audit purposes.

This separation supports stronger incident response, better evidence integrity, and clearer ownership of security operations.
