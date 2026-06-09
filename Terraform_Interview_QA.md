# Terraform Interview Questions & Answers
## Up to 50 Important & Frequently Asked Questions with Real-World Examples

---

## 1. What is Terraform?
**A:** Terraform is an open-source infrastructure-as-code (IaC) tool by HashiCorp that lets you define, preview, and apply cloud and on-prem resources using declarative configuration files.

Real-World: Use Terraform to provision AWS networking, compute, and managed services consistently across environments.

---

## 2. What are providers in Terraform?
**A:** Providers are plugins that interact with APIs (AWS, Azure, GCP, Kubernetes). They expose resources and data sources.

Real-World: The AWS provider lets you create aws_instance, aws_vpc, aws_s3_bucket resources.

---

## 3. What is the Terraform state file and why is it important?
**A:** The state file (terraform.tfstate) stores the current mapping between Terraform resources and remote objects. Terraform uses it to plan and apply changes.

Real-World: Use remote state (S3 backend with DynamoDB locking) to allow teams to collaborate without state conflicts.

---

## 4. What backends are commonly used?
**A:** Common backends: local, S3 (with DynamoDB for locking), GCS, AzureRM, Terraform Cloud. Backends store state and can handle locking.

Real-World: S3 + DynamoDB is the de-facto pattern for AWS teams to share state with locking.

---

## 5. How do you manage state locking?
**A:** Use a backend that supports locking (e.g., S3 backend with DynamoDB table) or Terraform Cloud/Enterprise which provides locking.

Real-World: Create a DynamoDB table with a `LockID` partition key and reference it in backend config to prevent concurrent apply runs.

---

## 6. What are modules in Terraform?
**A:** Modules are reusable sets of Terraform configuration (collections of .tf files). The root module is the configuration you run. Reuse by creating child modules.

Real-World: Create a `vpc` module and reuse it across dev/stage/prod with different variables.

---

## 7. What is the difference between module and provider?
**A:** Provider is the API plugin (AWS, Azure). Module is a grouping of resources and configuration used to encapsulate infrastructure patterns.

---

## 8. How do you pass variables to modules?
**A:** Use input variables in the module block: `module "vpc" { source = "./modules/vpc" cidr = var.cidr }` and define `variable "cidr" {}` in the module.

Real-World: Pass environment-specific CIDR, tags, or instance types into the module.

---

## 9. What's the difference between `count` and `for_each`?
**A:** `count` creates resources by index (list length or number). `for_each` iterates over a map or set of strings and creates named instances. Use `for_each` for stable addressing when resources may be added/removed.

Real-World: Use `for_each` to create security group rules keyed by name to avoid index-based drift.

---

## 10. How to handle secrets in Terraform?
**A:** Avoid hardcoding secrets. Use external secret managers (AWS Secrets Manager, SSM Parameter Store) accessed via data sources, or use environment variables/CI secret stores and mark sensitive outputs.

Real-World: Store DB credentials in Secrets Manager and fetch them in Terraform using `data "aws_secretsmanager_secret_version"`.

---

## 11. What is `terraform init`?
**A:** Initializes a working directory: downloads provider plugins, sets up backend configuration, and initializes modules.

---

## 12. What is `terraform plan` vs `terraform apply`?
**A:** `plan` shows an execution plan without making changes. `apply` executes the plan (optionally using the `-auto-approve` flag).

Real-World: Always run `plan` in CI to detect drift or undesired changes before `apply`.

---

## 13. What is `terraform fmt` and `terraform validate`?
**A:** `fmt` formats code to canonical style. `validate` checks configuration for syntactic/semantic correctness without contacting providers.

---

## 14. How do you import existing resources?
**A:** Use `terraform import <address> <id>` to map an existing remote resource into state, then add corresponding resource block in config.

Real-World: Import a pre-existing RDS instance by `terraform import aws_db_instance.prod db-12345` then reconcile configuration.

---

## 15. What are workspaces and when to use them?
**A:** Workspaces provide multiple states for a single configuration. Often used for lightweight environment separation (dev/stage/prod) but can be misused; many prefer separate state per environment.

Real-World: Use workspaces for feature branches or ephemeral environments; for prod, prefer separate directories/backends.

---

## 16. How do you version control Terraform code?
**A:** Store `.tf` files in Git. Keep state out of VCS. Use `.gitignore` for local `terraform.tfstate`. Tag releases and use semantic versioning for modules.

Real-World: Use GitHub + protected branches and PR-based reviews for infra changes.

---

## 17. How to enforce policy and governance?
**A:** Use tools like Terraform Cloud/Enterprise Sentinel, Open Policy Agent (OPA) with Conftest, or pre-commit hooks to enforce tagging, instance sizes, or prohibited services.

Real-World: A policy denies public S3 buckets by scanning plan or running pre-commit checks.

---

## 18. What are data sources?
**A:** Data sources read existing information from providers (e.g., AMI IDs, VPCs) to use in configs.

Real-World: Use `data "aws_ami"` to pick the latest AMI by filters rather than hardcoding AMI IDs.

---

## 19. How do you handle provider versioning?
**A:** Pin provider versions in the `required_providers` and `terraform` blocks, e.g., `required_providers { aws = { source = "hashicorp/aws" version = ">= 4.0, < 5.0" } }`.

Real-World: Pin versions to avoid breaking provider changes during CI runs.

---

## 20. What is `lifecycle` block used for?
**A:** `lifecycle` controls resource creation and deletion behavior: `prevent_destroy`, `create_before_destroy`, and `ignore_changes`.

Real-World: Use `prevent_destroy` on critical resources like production databases to prevent accidental deletion.

---

## 21. What is `provider alias` and when is it used?
**A:** Aliased providers allow configuring multiple instances of the same provider (e.g., multiple AWS accounts/regions). Use `provider "aws" { alias = "shared" region = "us-east-1" }` and reference with `provider = aws.shared` in modules.

Real-World: Deploy resources into two AWS accounts from the same Terraform run using provider aliases.

---

## 22. How do you share modules?
**A:** Publish modules to a registry (Terraform Registry or private registry), or reference modules via Git, local paths, or S3.

Real-World: Maintain a private module registry for company-wide VPC, IAM, and ECS modules.

---

## 23. What is `terraform taint` / `untaint`?
**A:** `taint` marks a resource for recreation on the next `apply`. `untaint` removes that mark. (Note: newer Terraform versions use `terraform apply -replace`.)

Real-World: Taint an instance to force replacement after a critical compromise.

---

## 24. How do you perform zero-downtime deployments?
**A:** Use `create_before_destroy` for resources that can be replaced, blue/green deployments, or use immutable infra patterns (new resources + switch traffic via Load Balancer or DNS).

Real-World: Replace autoscaling group instances via new launch template and update ALB target group, draining old instances.

---

## 25. How to manage multiple environments (dev/stage/prod)?
**A:** Use separate backends per environment, parameterize with variables, and use CI pipelines to deploy. Avoid using workspaces as the only means for environment separation in complex orgs.

Real-World: Maintain `environments/{dev,stg,prod}` directories each with backend config pointing to separate S3 buckets/prefixes.

---

## 26. What is drift and how to detect it?
**A:** Drift occurs when actual infrastructure diverges from Terraform state/config. Detect using `terraform plan` (shows changes) or `terraform refresh` and use drift detection tools.

Real-World: Detect a manual change to an EC2 instance type that is different from Terraform config using `terraform plan` and then decide whether to import or revert the change.

---

## 27. How to roll back changes?
**A:** Terraform doesn't have built-in rollbacks; use version-controlled configurations, state snapshots, or separate automation to re-apply a previous configuration. Terraform Cloud provides run history to revert.

Real-World: Revert a Git commit and re-run `apply` to restore previous infrastructure state.

---

## 28. What's the use of `terraform state` commands?
**A:** Manage state: `list`, `show`, `mv`, `rm`, `pull`, `push`, `replace-provider`. Useful for state maintenance and migrations.

Real-World: Use `terraform state mv` to move resources into modules after refactoring, and `state rm` to remove orphaned resources.

---

## 29. How do you test Terraform code?
**A:** Use `terraform validate`, `terraform plan`, unit tests with Terratest (Go), kitchen-terraform, or static analysis with TFLint, Checkov, or tfsec for security checks.

Real-World: Use Terratest in CI to deploy ephemeral infra, run assertions, and destroy.

---

## 30. What's the difference between `apply -auto-approve` and a manual apply?
**A:** `-auto-approve` bypasses interactive approval prompt; use cautiously in automated CI where plan checks are performed beforehand.

Real-World: CI pipeline runs `terraform plan` and gates approval, then a deploy job runs `apply -auto-approve` against a verified plan.

---

## 31. How do you handle provider credentials in CI?
**A:** Use environment variables (AWS_ACCESS_KEY_ID, AWS_SECRET_ACCESS_KEY), assume-role via `aws sts assume-role` or IAM roles for EC2/CodeBuild. Store secrets in CI secret store.

Real-World: Use GitHub Actions OIDC to assume an IAM role without long-lived credentials.

---

## 32. What are dynamic blocks?
**A:** Dynamic blocks allow generating nested blocks programmatically with `for_each`, e.g., dynamic security group rules.

Real-World: Generate complex list of `ingress` rules for a security group from a map variable.

---

## 33. When to use `depends_on`?
**A:** Explicitly specify resource dependencies when Terraform can't infer them (rare). Use to control creation order or ensure dependent resources exist.

Real-World: Ensure IAM role creation finishes before invoking a null_resource provisioner that needs the role's ARN.

---

## 34. What are provisioners and why are they discouraged?
**A:** Provisioners run scripts on created resources (remote-exec, local-exec). Discouraged because they introduce non-idempotent operations and can break reproducibility; prefer configuration management or user-data.

Real-World: Use user-data or Systems Manager Run Command instead of provisioners for bootstrapping instances.

---

## 35. How do you manage Terraform around API rate limits and large plans?
**A:** Use batching to limit resource creation concurrency via provider settings, split large plans into smaller modules, and use `parallelism` flag for `apply`.

Real-World: Large org separates infra into network, security, and application modules to reduce API throttling.

---

## 36. How to handle cross-account deployments?
**A:** Use provider aliases with different credentials or assume-role patterns. Configure backend per account and use CI to assume role into the target account.

Real-World: Deploy central networking in account A and app infra in account B by configuring two providers and assuming roles.

---

## 37. How do you upgrade Terraform versions safely?
**A:** Read upgrade guides, test in non-prod environments, use `terraform 0.13upgrade` if migrating, and run `fmt`, `validate`, and full `plan` to detect changes.

Real-World: Run upgrade in staging and review plans for breaking changes before applying to production.

---

## 38. What is `sensitive` attribute on outputs and variables?
**A:** Marking outputs or variables as `sensitive = true` prevents them from being shown in CLI output or logs. It helps protect secrets but doesn't encrypt state by itself.

Real-World: Mark DB password outputs as sensitive and use encrypted backends for state.

---

## 39. How to migrate state between backends?
**A:** Use `terraform init -migrate-state` or `terraform state` commands to move state manually. Ensure exclusive access and backup before migration.

Real-World: Migrate local state to S3 with DynamoDB locking during onboarding to remote backend.

---

## 40. How to reference outputs from another workspace or remote state?
**A:** Use `terraform_remote_state` data source with backend config to fetch outputs from another state, or use module outputs when calling modules.

Real-World: Network module in one repo stores VPC IDs in remote state; application repo fetches VPC ID using `terraform_remote_state`.

---

## 41. What are the best practices for module design?
**A:** Keep modules small and focused, use clear inputs/outputs, provide defaults, document variables via README, version modules, and avoid embedding environment-specific configs.

Real-World: Create a `module/vpc` that handles network only; avoid including app-specific resources in the VPC module.

---

## 42. How to handle sensitive provider configuration (like access credentials)?
**A:** Use environment variables, CI secret stores, or instance/role-based credentials. Avoid storing secrets in `.tf` files. Use Vault or AWS SSM/Secrets Manager for secrets retrieval at runtime.

---

## 43. How do you handle EC2 user-data and bootstrap scripts?
**A:** Keep user-data scripts idempotent, store heavy scripts in S3 and fetch at boot, or use configuration management tools. Avoid complex logic in Terraform provisioners.

Real-World: User-data fetches bootstrap script from S3 and runs a setup that registers the instance with the autoscaling lifecycle hook.

---

## 44. Explain Terraform Registry and module versioning.
**A:** Terraform Registry hosts public/private modules. Version modules using semantic versioning and reference modules with `source = "app.terraform.io/org/module/aws"` and `version = "~> 1.2"`.

Real-World: Pin module versions in production to avoid unexpected changes.

---

## 45. How to do blue/green deployments with Terraform?
**A:** Create two sets of resources (blue & green), switch traffic via load balancer target group or Route53 weighted records, then destroy the old set after verification.

Real-World: Deploy new autoscaling group and change ALB target group attachment in Terraform; use DNS TTLs or ALB weights.

---

## 46. How to manage IAM policy documents in Terraform?
**A:** Use `aws_iam_policy_document` data source to build JSON documents programmatically and avoid templating inline JSON. Keep least privilege in mind.

Real-World: Build dynamic IAM policies for Lambda functions with only required permissions using policy document data source.

---

## 47. What are Terraform cloud/enterprise features useful for teams?
**A:** Remote runs, state management, policy checks (Sentinel), private module registry, VCS integration, and role-based access control.

Real-World: Use Terraform Cloud to centralize runs, enforce policies, and share modules securely.

---

## 48. How to use `null_resource` effectively?
**A:** `null_resource` can run local-exec/remote-exec provisioners keyed on triggers. Useful for orchestration but avoid heavy reliance.

Real-World: Use `null_resource` to run a script after applying infrastructure, driven by file or template triggers.

---

## 49. How to handle complex maps and lists in variables?
**A:** Use object types and maps with clear schemas (`variable "vpcs" { type = map(object({ cidr = string, azs = list(string) })) }`). Validate shapes with `validation` blocks.

Real-World: Pass a map of environments to a module to create multiple VPCs programmatically.

---

## 50. Common interview mistakes and tips
**A:**
- Treating Terraform as a scripting tool rather than declarative IaC
- Storing state in VCS
- Using provisioners instead of configuration management
- Not pinning provider/module versions
- Ignoring state backups and locking

Tips:
- Always run `terraform fmt` and `validate`
- Run `plan` and review diffs
- Use remote state with locking
- Use small, well-documented modules
- Keep secrets out of `.tf` files

---

## Quick Checklist for Interviews
```
✓ Understand state, backends, and locking (S3 + DynamoDB)
✓ Know modules, inputs, outputs, and versioning
✓ Know `plan`, `apply`, `fmt`, `validate`, `import`
✓ Explain provider version pinning and `required_providers`
✓ Describe strategies for secrets, CI credentials, and remote runs
✓ Discuss testing (Terratest, tflint, tfsec) and policy enforcement
✓ Explain `count` vs `for_each`, `dynamic` blocks, and lifecycle rules
✓ Describe strategies for multi-account and multi-env deployments
```

---

If you want, I can:
- Add this file to your repository now as `Terraform_Interview_QA.md`.
- Expand any question with CLI examples, code snippets, or Terraform sample modules.
- Create a README index and link all QA files.
- Convert these into flashcards or printable notes.

Which would you like next?