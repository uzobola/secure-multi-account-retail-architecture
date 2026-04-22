# Sample Network Segmentation Pattern

## Purpose

This document provides a simplified example of how network segmentation is applied in the secure multi-account AWS retail architecture.

The goal is to show how public and private network boundaries, subnet placement, and security group design work together to reduce unnecessary exposure and support a private-by-default workload model.

---

## High-Level Segmentation Model

Within the **Retail-Prod** account, the workload is segmented into multiple layers:

- edge and ingress layer
- application layer
- data layer
- management and visibility components

Only the approved ingress path is internet-facing. Application and data services remain private.

---

## Traffic Flow

The intended traffic path is:

**Route 53 → CloudFront → AWS WAF → Application Load Balancer → EC2 Auto Scaling Group → RDS**

This flow reflects the main design principle: expose only the minimum required path while keeping application and database tiers private.

---

## Subnet Pattern

### Public subnets
Public subnets are limited to components that need internet-facing routing behavior.

Examples:
- Application Load Balancer
- NAT Gateway, if used
- edge-related ingress path components where applicable

### Private application subnets
Private application subnets host the EC2 instances in the Auto Scaling Group.

These workloads:
- are not directly reachable from the internet
- receive traffic only from the ALB
- use scoped outbound access where needed
- rely on instance roles and approved service paths

### Private data subnets
Private data subnets host the database tier.

Examples:
- RDS PostgreSQL or Aurora PostgreSQL

These workloads:
- do not accept direct public traffic
- accept only approved traffic from the application tier
- are protected by narrow security group rules and encryption controls

---

## Security Group Pattern

This architecture uses security groups to explicitly define allowed paths between tiers.

### ALB security group
Allows:
- inbound HTTPS from approved internet-facing entry path
- outbound traffic to the application tier on the approved application port

### Application tier security group
Allows:
- inbound traffic only from the ALB security group
- outbound traffic only to required destinations such as:
  - database security group
  - CloudWatch Logs
  - Secrets Manager
  - KMS
  - approved internal or AWS service endpoints

### Database security group
Allows:
- inbound traffic only from the application tier security group on the approved database port
- no direct public access
- no broad inbound rules

---

## Example Segmentation Principles

### Principle 1: No direct internet access to the app tier
The application instances should not be directly reachable from the internet. Internet-facing traffic terminates through the approved edge and load-balancing path.

### Principle 2: No public database exposure
The database tier should remain private and only accept traffic from the application tier.

### Principle 3: Minimize east-west access
Only explicitly approved traffic paths should exist between internal tiers. Broad lateral access should be avoided.

### Principle 4: Keep ingress simple and reviewable
The fewer public entry paths that exist, the easier the architecture is to review, secure, and monitor.

---

## Private Service Access and Endpoint Usage

Where appropriate, this architecture can use private service access patterns to reduce internet dependency.

Examples include:
- VPC endpoints for AWS services such as Secrets Manager
- VPC endpoints for CloudWatch or SSM-related access patterns
- private connectivity patterns that reduce reliance on broad outbound internet access

This supports a stronger private-by-default model and aligns with zero-trust and segmentation goals.

---

## Example Review Questions

When reviewing segmentation in this architecture, useful questions include:

- Which components are internet-facing, and why?
- Which tiers remain private?
- What traffic is allowed between the ALB and app tier?
- What traffic is allowed between the app tier and database tier?
- Are outbound paths tightly scoped or broadly open?
- Are there private endpoint opportunities to reduce internet dependency?
- Does the traffic flow match the intended trust boundaries?

---

## Key Takeaway

Network segmentation in this architecture is not only about subnet placement.

It is about making trust boundaries visible and enforceable: limiting public exposure, narrowing allowed traffic paths, keeping sensitive tiers private, and making the intended design easy to reason about and review.