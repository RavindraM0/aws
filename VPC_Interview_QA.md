# AWS VPC Interview Questions & Answers
## Important & Frequently Asked Questions with Real-World Examples

---

## 1. What is a VPC?

**Q:** What is VPC?

**A:** A Virtual Private Cloud (VPC) is a logically isolated virtual network in the AWS cloud where you launch AWS resources. You control IP address ranges, subnets, routing, and network gateways.

Real-World Example:
```
Production environment:
- Custom VPC with CIDR 10.0.0.0/16
- Separate subnets per AZ: 10.0.1.0/24, 10.0.2.0/24, 10.0.3.0/24
- Public subnets for load balancers, private subnets for application servers and databases
```

---

## 2. Public vs Private Subnets

**Q:** What's the difference between public and private subnets?

**A:** Public subnets have a route to an Internet Gateway (IGW) and can host resources with direct internet access. Private subnets have no direct IGW route and are used for internal resources; internet access is provided via a NAT Gateway or instance.

Real-World Example:
```
Web tier in public subnet (behind ALB), application & DB tiers in private subnets. App servers fetch updates via NAT Gateway.
```

---

## 3. Route Tables, Internet Gateway, NAT

**Q:** How do instances in a private subnet access the internet?

**A:** Private instances route outbound traffic through a NAT Gateway (managed) or NAT instance in a public subnet. The NAT translates private IPs to the NAT's public IP for outbound connectivity.

Trade-offs:
- NAT Gateway: managed, highly available within an AZ, charged per hour and data processed
- NAT Instance: cheaper but requires management and scaling

---

## 4. Security Groups vs NACLs

**Q:** When should you use Security Groups vs Network ACLs?

**A:** Security Groups are instance-level, stateful firewalls with allow rules only and are the primary mechanism to control access to instances. NACLs are subnet-level, stateless and can allow or deny traffic; they provide an additional boundary.

Real-World Example:
```
Security Group: Allow inbound 443 from ALB security group only
NACL: Deny specific IP ranges known to be malicious at subnet edge
```

---

## 5. VPC Peering vs Transit Gateway

**Q:** When to use VPC Peering vs Transit Gateway?

**A:** VPC Peering is best for simple, one-to-one connectivity between two VPCs. Transit Gateway is better for hub-and-spoke architectures connecting many VPCs and on-prem networks at scale.

Real-World Example:
```
Startup with 2 VPCs: use peering. Enterprise with 20+ VPCs across accounts: use Transit Gateway for simplified routing.
```

---

## 6. VPC Endpoints (Interface & Gateway)

**Q:** What are VPC endpoints and why use them?

**A:** VPC endpoints allow private connectivity to AWS services without internet access. Gateway endpoints (S3, DynamoDB) add route table entries; Interface endpoints create ENIs for other services.

Real-World Example:
```
Private applications access S3 via a gateway endpoint — no internet egress, lower cost, better security.
```

---

## 7. VPN vs Direct Connect

**Q:** When to use VPN vs Direct Connect?

**A:** VPN (IPSec over internet) is quick to set up and suitable for smaller throughput. Direct Connect provides dedicated network links with consistent lower latency and reduced egress costs for large, steady traffic.

Real-World Example:
```
Retail chain uses Direct Connect for nightly bulk data transfer to AWS from on-prem data center for predictable throughput and lower costs.
```

---

## 8. CIDR Planning Best Practices

**Q:** What are good CIDR planning practices?

**A:** Use non-overlapping CIDRs across peered or on-prem networks, reserve space for growth, avoid using RFC1918 ranges that conflict with customer networks, and prefer /16 or /20 for large orgs with many subnets.

Real-World Example:
```
Large org: allocate /16 per environment (prod/dev/stage) and /24 per AZ-subnet to avoid exhaustion.
```

---

## 9. High Availability Design

**Q:** How to design HA in VPC?

**A:** Distribute resources across multiple AZs, deploy redundant NAT Gateways and load balancers across AZs, and use Multi-AZ services for databases.

Real-World Example:
```
Deploy ALB in all AZs, app instances in private subnets across AZs, NAT Gateway per AZ to avoid cross-AZ charges and single point of failure.
```

---

## 10. Bastion Host vs Session Manager

**Q:** How to access private instances securely?

**A:** Use an SSH bastion host (tight security groups, restricted IPs) or prefer AWS Systems Manager Session Manager to avoid opening SSH ports and use IAM-based access controls.

Real-World Example:
```
Replace public bastions with Session Manager to eliminate SSH exposure and centralize audit logs in CloudWatch/CloudTrail.
```

---

## 11. VPC Flow Logs

**Q:** What are VPC Flow Logs used for?

**A:** Capture IP traffic to/from network interfaces for monitoring, security analysis, and troubleshooting. Send logs to CloudWatch Logs or S3 for analysis.

Real-World Example:
```
Use Flow Logs with Athena to analyze suspicious outbound traffic and create alerts when unusual destinations are observed.
```

---

## 12. Common Pitfalls

**A:**
- Overly permissive security groups (0.0.0.0/0)
- Single AZ NAT Gateway (single point of failure)
- CIDR overlap preventing peering or Direct Connect
- Exposing management ports to the internet

---

## Quick Checklist

```
✓ Design multi-AZ subnets
✓ Keep databases in private subnets
✓ Use VPC endpoints for AWS services
✓ Use Transit Gateway for many-VPC architectures
✓ Implement least-privilege security groups
✓ Enable VPC Flow Logs for auditing
✓ Prefer Session Manager over SSH for access
```
