# Sample IAM Patterns

## Purpose

These examples show how IAM patterns support secure administration and workload access in the multi-account retail architecture.

## Pattern 1: Human administrative access with IAM Identity Center

### Goal
Provide auditable, centralized, least-privilege administrative access into workload accounts without relying on long-lived IAM users.

### Pattern
1. A human administrator authenticates through IAM Identity Center.
2. The user is assigned a permission set appropriate to their role.
3. The permission set maps to an account role in the target AWS account.
4. The administrator assumes the role only when needed.
5. Administrative activity is logged through CloudTrail.

### Why this matters
This pattern improves:
- centralized access management
- separation of duties
- auditability
- reduced credential sprawl

### Example high-level policy idea
- platform admin access to Retail-NonProd
- more limited, controlled access to Retail-Prod
- security review and logging around elevated access

---

## Pattern 2: EC2 application role for retail workload

### Goal
Allow the retail application running on EC2 to access only the AWS services it needs.

### Pattern
The EC2 instances in the Auto Scaling Group use an instance profile role with scoped permissions such as:

- read application secrets from Secrets Manager
- write logs to CloudWatch Logs
- read or write to a specific S3 bucket if required
- decrypt data with an approved KMS key if needed

### Example pseudo-policy scope
- `secretsmanager:GetSecretValue` only for the specific application secret
- `logs:CreateLogStream` and `logs:PutLogEvents` only for the app log group
- `s3:GetObject` or `s3:PutObject` only for the approved bucket path
- `kms:Decrypt` only for the specific key required by the application

### Why this matters
This pattern reduces blast radius by avoiding wildcard permissions and limiting the application role to only the resources it actually needs.

---

## Key Takeaway

IAM in this architecture is not treated as a standalone topic. It is part of the trust-boundary design, operational model, and security posture of the entire platform.