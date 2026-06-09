# AWS EC2 Interview Questions & Answers
## Important & Frequently Asked Questions with Real-World Examples

---

## 1. What is AWS EC2 and how does it work?

**Q:** What is AWS EC2?

**A:** EC2 (Elastic Compute Cloud) is a web service that provides resizable compute capacity in the cloud. It allows you to launch virtual machines (instances) on demand.

**Real-World Example:**
```
E-commerce platform scenario:
- During normal days: Run 5 EC2 instances to handle traffic
- During Black Friday: Auto-scale to 50 instances to handle peak traffic
- After sale: Scale back to 5 instances to reduce costs
This elasticity saves money and improves performance.
```

---

## 2. Difference between On-Demand, Reserved, and Spot Instances

**Q:** What are the different EC2 pricing models?

**A:** 
1. **On-Demand**: Pay per hour/second. No commitment. Highest cost.
2. **Reserved Instances**: Commit for 1-3 years. Get 40-70% discount.
3. **Spot Instances**: Bid for unused capacity. Get 70-90% discount. Can be interrupted.

**Real-World Example:**
```
Web Application Company:
- Production Database Server: Reserved Instance (stable workload) → Save 60%
- Web Servers: On-Demand (variable traffic) → Flexibility
- Batch Processing Jobs: Spot Instances (fault-tolerant) → Save 85%

Annual Savings: ~$50,000-$100,000 with smart pricing mix
```

---

## 3. What is an AMI (Amazon Machine Image)?

**Q:** What is an AMI? How is it used?

**A:** An AMI is a pre-configured template containing:
- Operating System (Linux, Windows)
- Applications pre-installed
- Configuration settings
- Permissions

**Real-World Example:**
```
Company X runs 100 web servers. Instead of manually configuring each:
1. Create custom AMI with:
   - Ubuntu OS
   - Node.js pre-installed
   - Application code
   - Security patches
2. Launch 100 instances from this AMI in 5 minutes
3. All servers have identical configuration
Result: Consistency, faster deployment, reduced errors
```

---

## 4. EC2 Instance States and Lifecycle

**Q:** What are EC2 instance states and their meaning?

**A:**
- **Running**: Instance is active and running
- **Stopped**: Instance is turned off (data preserved, not charged for compute)
- **Terminated**: Instance is deleted (cannot be restarted)
- **Pending**: Instance is starting
- **Stopping**: Instance is shutting down

**Real-World Example:**
```
Development Team Workflow:
Morning: Developer stops 10 test instances (saves ~50% costs)
During work: Instances are in STOPPED state
Evening: Dev starts instances again
Result: Only pay for actual working hours (~$500/month saved)
```

---

## 5. Security Groups and Network ACLs

**Q:** What's the difference between Security Groups and NACLs?

**A:**
| Feature | Security Group | NACL |
|---------|---------------|------|
| Level | Instance level | Subnet level |
| Rules | Allow rules only | Allow & Deny |
| Stateful | Yes (remember connections) | Stateless |
| Default | Allow nothing (Deny All) | Allow all (Allow All) |

**Real-World Example:**
```
E-commerce Website:
Security Group (Instance level):
- Allow port 80 (HTTP) from anywhere
- Allow port 443 (HTTPS) from anywhere
- Allow port 22 (SSH) from company IP only
- Deny port 3306 (MySQL) from internet

NACL (Subnet level):
- Allow port 80/443 from anywhere
- Allow all outbound traffic
- Additional layer of protection
```

---

## 6. Elastic IPs vs Public IPs

**Q:** What's the difference between Elastic IP and Public IP?

**A:**
- **Public IP**: Changes when instance stops/restarts. Free. Auto-assigned.
- **Elastic IP**: Static IP that persists. Associated with instance. Can be transferred.

**Real-World Example:**
```
Production Database Server:
- Need: Static Elastic IP so applications always connect to same address
- Without Elastic IP: Server restarts → IP changes → Applications break
- With Elastic IP: Server restarts → IP stays same → No downtime

Cost: Elastic IP is free when in use, but charged when not associated
```

---

## 7. Auto Scaling Groups

**Q:** How does Auto Scaling work in AWS?

**A:** Auto Scaling automatically increases/decreases EC2 instances based on demand using policies.

**Real-World Example:**
```
Video Streaming Platform:
Scaling Policy:
- If CPU usage > 70% for 5 minutes → Add 5 more instances
- If CPU usage < 30% for 10 minutes → Remove 2 instances
- Always maintain minimum 10 instances
- Never exceed 100 instances

Scenario:
8 PM (Peak Hours): 
- CPU jumps to 85% → Auto add instances → 40 instances running
- Cost: ~$200/hour

2 AM (Off-Peak):
- CPU at 20% → Auto remove instances → 10 instances running
- Cost: ~$50/hour

Monthly Savings: Automatic cost optimization without manual intervention
```

---

## 8. EBS (Elastic Block Store) vs Instance Store

**Q:** What's the difference between EBS and Instance Store?

**A:**
| Feature | EBS | Instance Store |
|---------|-----|-----------------|
| Persistence | Data persists after stop | Data lost after stop |
| Performance | Slower (network attached) | Faster (local disk) |
| Cost | Pay for volume size | Included with instance |
| Use Case | Databases, important files | Caching, temp files |

**Real-World Example:**
```
Banking Application:
- Customer Database: EBS (data must never be lost)
- Cache Server: Instance Store (can be regenerated)
- User Files: EBS (critical data)
- Session Cache: Instance Store (temporary, fast)

If server crashes:
- EBS data: Safe and recoverable
- Instance Store data: Lost forever
```

---

## 9. Load Balancing

**Q:** What are types of load balancers and when to use each?

**A:**
1. **Classic LB**: Legacy, simple TCP/HTTP
2. **ALB (Application LB)**: Layer 7, best for web apps, microservices
3. **NLB (Network LB)**: Layer 4, ultra-high performance, millions of requests/sec

**Real-World Example:**
```
Company with Multiple Services:

ALB (Application Load Balancer):
- Route /api → API servers
- Route /images → Image servers
- Route /static → Static content
- Hostname-based routing: api.example.com → API servers

NLB (Network Load Balancer):
- Real-time trading platform: 1M+ requests/sec
- Gaming servers: Ultra-low latency
- IoT: Millions of device connections
```

---

## 10. How to ensure High Availability

**Q:** How do you design highly available EC2 infrastructure?

**A:** Multi-layered approach:
1. **Multi-AZ Deployment**: Spread instances across multiple Availability Zones
2. **Auto Scaling**: Replace failed instances automatically
3. **Load Balancing**: Distribute traffic
4. **Health Checks**: Detect and replace unhealthy instances
5. **RTO/RPO**: Define recovery requirements

**Real-World Example:**
```
SaaS Platform (99.99% Uptime requirement):

Architecture:
- Region: us-east-1 (main)
- AZ-1: 5 instances (us-east-1a)
- AZ-2: 5 instances (us-east-1b)
- Load Balancer: Routes traffic across both AZs
- Auto Scaling: Min 10, Max 30 instances
- RDS Multi-AZ: Database in two AZs

Failure Scenario:
- AZ-1 data center fails
- Load balancer automatically redirects to AZ-2
- Auto Scaling launches new instances in AZ-1
- Users experience 0 downtime
- Platform remains at 99.99% availability
```

---

## 11. Monitoring and Logging

**Q:** How do you monitor EC2 instances?

**A:** Key tools:
- **CloudWatch**: Metrics (CPU, network, disk)
- **CloudTrail**: API audit logs
- **VPC Flow Logs**: Network traffic analysis
- **Systems Manager**: Agent-based monitoring

**Real-World Example:**
```
Production Monitoring Setup:
- CPU Alert: > 80% for 5 min → Send Slack notification
- Disk Space Alert: < 10% free → Auto-trigger scaling
- Network Alert: > 1Gbps → Investigate DDoS
- Memory Alert: > 90% → Restart service

Benefits:
- Proactive problem detection
- Faster incident response
- Historical trending for capacity planning
```

---

## 12. Cost Optimization Tips

**Q:** How do you optimize EC2 costs?

**A:**
1. Right-sizing: Use correct instance type for workload
2. Use Reserved Instances for predictable workloads
3. Use Spot Instances for batch jobs
4. Stop unused instances
5. Delete unused EBS volumes and Elastic IPs
6. Use Auto Scaling to match demand

**Real-World Example:**
```
Company spending $100K/month on EC2:

Optimization Steps:
1. Right-sizing: Changed m5.2xlarge → m5.large (overprovisioned)
   Savings: $30K/month
   
2. Reserved Instances: 70% workload on 1-year RIs
   Savings: $20K/month
   
3. Spot Instances: Batch jobs on Spot
   Savings: $10K/month
   
4. Stopped unused instances: Dev environments stopped after hours
   Savings: $15K/month

Total Savings: $75K/month (75% reduction!)
```

---

## 13. Common Interview Mistakes

**Q:** What are common mistakes in EC2 design?

**A:**
1. ❌ Using single instance without backup
2. ❌ Storing sensitive data in Instance Store
3. ❌ Not using Elastic IPs when needed
4. ❌ Ignoring security groups configuration
5. ❌ No monitoring or alerting setup
6. ❌ Over-provisioning instances
7. ❌ Not using Auto Scaling for variable workloads

---

## 14. Quick Reference Checklist

```
✓ Define instance type based on workload
✓ Choose appropriate pricing model (On-Demand/Reserved/Spot)
✓ Create custom AMI for consistent deployments
✓ Configure Security Groups properly (least privilege)
✓ Use Elastic IPs for static IP needs
✓ Set up Auto Scaling for variable workloads
✓ Implement Multi-AZ for high availability
✓ Enable CloudWatch monitoring
✓ Use EBS for persistent data
✓ Optimize costs regularly
✓ Document architecture and runbooks
```

---

## 15. Popular Instance Types (Use Cases)

```
General Purpose (T3, M5):
- Web servers, small databases, development

Compute Optimized (C5):
- High-performance processors, batch processing, gaming

Memory Optimized (R5):
- In-memory databases, caching, big data analytics

Storage Optimized (I3):
- NoSQL databases, data warehousing, Elasticsearch

GPU Instances (P3, G4):
- Machine learning, graphics rendering, scientific computing
```

---

## Summary

**Key Points to Remember:**
1. EC2 provides flexible, scalable computing
2. Choose right pricing model to optimize costs
3. Design for high availability and disaster recovery
4. Always monitor and alert
5. Use Auto Scaling for variable workloads
6. Security is multi-layered (Security Groups + NACLs)
7. Right-size instances for your workload
8. Implement monitoring and logging from day one

