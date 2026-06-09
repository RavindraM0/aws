# Kubernetes on AWS (EKS) Interview Questions & Answers
## Important & Frequently Asked Questions with Real-World Examples

---

## 1. What is Amazon EKS?

**Q:** What is EKS?

**A:** Amazon Elastic Kubernetes Service (EKS) is a managed Kubernetes control plane. AWS runs and patches the control plane; you manage worker nodes (EC2 or Fargate) and cluster configuration.

Real-World: Run microservices in containers using EKS; AWS manages API servers and etcd, reducing operational overhead.

---

## 2. EKS vs Self-Managed Kubernetes

**Q:** Why choose EKS over self-managed k8s?

**A:** EKS reduces operational burden (control plane availability, upgrades, etcd backups). Self-managed gives full control but requires more operational effort.

Real-World: Startups often choose EKS for faster time-to-market; large companies may self-manage for custom requirements.

---

## 3. Node Types: Managed Node Groups, Self-Managed, and Fargate

**Q:** What node options exist?

**A:** Managed Node Groups (auto-managed EC2 groups by EKS), Self-Managed Nodes (EC2 instances you control), and Fargate (serverless compute for pods).

Real-World: Use Managed Node Groups for standard workloads, Fargate for small bursty or single-tenant workloads, and self-managed nodes when custom AMIs or daemonsets are required.

---

## 4. Networking: VPC CNI, CNI Plugins, and IP Addressing

**Q:** How does pod networking work in EKS?

**A:** The aws-vpc-cni plugin assigns VPC IPs to pods, allowing pod-to-VPC routing. Alternatively use other CNIs (Calico, Cilium) for advanced policies or networking features.

Real-World: With aws-vpc-cni, plan CIDR sizes carefully to avoid IP exhaustion for large clusters.

---

## 5. IAM & Security: IRSA (IAM Roles for Service Accounts)

**Q:** How do pods get AWS permissions securely?

**A:** Use IRSA: create IAM roles for service accounts and annotate service accounts to assume them, instead of using node IAM roles for broad permissions.

Real-World: Give an S3 uploader pod a minimal role via IRSA instead of granting the node-wide role.

---

## 6. Ingress & Load Balancing

**Q:** How do you expose services externally?

**A:** Use Service type LoadBalancer (provisions NLB/ELB), or Ingress controllers (ALB/NGINX) for L7 routing. ALB ingress controller integrates with ALB for host/path-based routing.

Real-World: Use AWS ALB Ingress Controller to route api.example.com/* to backend services and static.example.com to a static content service.

---

## 7. Storage: EBS, EFS, and CSI

**Q:** How to provide persistent storage?

**A:** Use EBS via the EBS CSI driver for single-AZ block storage, or EFS CSI driver for multi-AZ POSIX shared storage. Use S3 for object storage via S3 clients or CSI S3 drivers.

Real-World: Stateful databases use EBS volumes per pod; shared file storage (e.g., CMS uploads) use EFS across pods in multiple AZs.

---

## 8. Autoscaling: Cluster Autoscaler and HPA/VPA

**Q:** How to scale pods and nodes?

**A:** Use Horizontal Pod Autoscaler (HPA) to scale pods, Vertical Pod Autoscaler (VPA) for resource tuning, and Cluster Autoscaler to scale node groups when pods can’t be scheduled.

Real-World: HPA scales web replicas by CPU or custom metrics; Cluster Autoscaler adds nodes when pending pods exist.

---

## 9. Observability: Logging, Metrics, and Tracing

**Q:** Recommended monitoring stack?

**A:** Prometheus + Grafana for metrics, Fluentd/Fluent Bit or CloudWatch agent for logs, and X-Ray/OpenTelemetry for tracing. EKS offers Container Insights as well.

Real-World: Install Prometheus and use kube-state-metrics to monitor pod/node health; send logs to CloudWatch Logs for centralized analysis.

---

## 10. CI/CD for Kubernetes on AWS

**Q:** How to deploy applications to EKS?

**A:** Use GitOps (Flux/ArgoCD), or pipelines (CodePipeline, GitHub Actions, Jenkins) that build images (ECR), push, and apply manifests or Helm charts.

Real-World: Use GitHub Actions to build Docker image, push to ECR, and update a HelmRelease managed by ArgoCD.

---

## 11. Helm and Package Management

**Q:** Why use Helm?

**A:** Helm packages charts (k8s manifests) and supports templating, releases, and values files for environment-specific configs. Use Helm for repeatable app installs.

Real-World: Use Helm charts for common services (Ingress, cert-manager, Prometheus) and maintain company charts in a private Helm repo.

---

## 12. Security Best Practices

**Q:** Key security practices for EKS?

**A:** Use IRSA for least-privilege, RBAC for Kubernetes access control, Pod Security Admission (or PodSecurityPolicies replacement) for pod restrictions, network policies (Calico/Cilium) to limit traffic, image scanning, and private ECR with lifecycle policies.

Real-World: Block outbound access for sensitive pods and enforce read-only root filesystems in pod security policies.

---

## 13. Upgrades and Version Management

**Q:** How to upgrade EKS clusters safely?

**A:** Upgrade control plane via EKS API, then upgrade node groups. Test in staging, use managed node groups or update launch templates for rolling upgrades, and cordon/drain nodes before replacement.

Real-World: Upgrade minor version in staging, then schedule maintenance window for production with draining and rolling node group replacements.

---

## 14. Troubleshooting Common Issues

**Q:** Where to start when pods fail?

**A:** Check `kubectl describe pod` and `kubectl logs`, verify events, check node resources, inspect CNI plugin logs, and examine scheduling (pending pods due to insufficient resources or taints).

Real-World: Pending pods often indicate IP exhaustion or insufficient node capacity—inspect node IP allocations and cluster autoscaler logs.

---

## 15. Cost Optimization

**Q:** How to reduce EKS costs?

**A:** Use right-sized instances, spot instances or spot node groups for fault-tolerant workloads, scale to zero where possible, use Fargate for small workloads, and consolidate workloads to reduce idle capacity.

Real-World: Move non-critical batch workers to spot node groups and use node taints/tolerations to schedule them appropriately.

---

## 16. Governance and Multi-Account Patterns

**Q:** Best practices for multi-account EKS deployments?

**A:** Use a centralized management account for shared services and CI, deploy clusters per account or per environment using automation (Terraform, eksctl), and standardize IAM roles, OIDC providers, and policies across accounts.

Real-World: Provision clusters via Terraform in each account and use GitOps for app deployment; manage shared logging/monitoring centrally.

---

## 17. Service Mesh

**Q:** When to use a service mesh (Istio, AWS App Mesh)?

**A:** Use service mesh when you need advanced observability, traffic control (canary, mirroring), mTLS, and policy enforcement. They add complexity and resource overhead.

Real-World: Introduce a mesh for microservices teams needing secure service-to-service encryption and fine-grained traffic routing for canary releases.

---

## 18. EKS Add-ons and Managed Services

**Q:** What are EKS add-ons?

**A:** EKS add-ons let AWS manage common components (CoreDNS, kube-proxy, VPC CNI) with versions tied to the cluster and simplified upgrades.

Real-World: Use EKS add-ons to keep cluster-critical components up-to-date with minimal effort.

---

## 19. Storage Classes and Dynamic Provisioning

**Q:** How to use StorageClasses?

**A:** Define StorageClasses (gp3, io2, efs) for dynamic PVC provisioning. Use parameters for volume type, IOPS, and encryption.

Real-World: Create a `standard` StorageClass for general workloads and `fast-io` for databases with io2 settings.

---

## 20. Common Interview Mistakes

**A:**
- Not planning IP/CIDR sizing for aws-vpc-cni
- Giving pods node-level IAM privileges instead of IRSA
- Heavy reliance on provisioners or cluster local state
- Not using network policies or RBAC

---

## Quick Checklist

```
✓ Use IRSA for pod permissions
✓ Plan VPC and IP addressing for pods
✓ Use managed node groups or Fargate for reduced maintenance
✓ Implement observability (Prometheus, CloudWatch, Fluent Bit)
✓ Use GitOps or pipelines for deployments
✓ Enforce RBAC and network policies
✓ Use Cluster Autoscaler and HPA for scalable workloads
```
