# AWS Lambda Interview Questions & Answers
## Important & Frequently Asked Questions with Real-World Examples

---

## 1. What is AWS Lambda?

**Q:** What is Lambda?

**A:** AWS Lambda is a serverless compute service that runs your code in response to events and automatically manages the underlying compute resources. You pay only for the compute time you consume.

Real-World Example:
```
Thumbnail generation:
- User uploads an image to S3
- S3 triggers a Lambda function that creates thumbnails and saves them back to S3
- No servers to manage; scales automatically with uploads
```

---

## 2. Lambda Triggers and Event Sources

**Q:** What can trigger Lambda functions?

**A:** S3 events, API Gateway, DynamoDB Streams, Kinesis Streams, CloudWatch Events (EventBridge), SNS, SQS, and more.

Example:
```
API Gateway -> Lambda: serverless API endpoints
SQS -> Lambda: decoupled processing of messages with retries
```

---

## 3. Cold Starts and Performance

**Q:** What are cold starts and how to mitigate them?

**A:** Cold starts occur when a new execution environment is created, adding latency. Mitigations: use Provisioned Concurrency, keep functions lightweight, prefer languages with lower start-up times, reuse connections (e.g., DB connections) outside handler.

Example:
```
Critical low-latency API uses Provisioned Concurrency to keep function instances warm and avoid cold starts.
```

---

## 4. Resource Limits and Timeouts

**Q:** What are typical limits?

**A:** Maximum timeout 15 minutes, memory up to 10,240 MB, ephemeral /tmp storage up to 512 MB (may change—check docs). Execution payloads and concurrent executions have limits; use reserved concurrency to control usage.

Example:
```
Long-running batch job (>15 min) should be moved to ECS/Fargate or split into smaller tasks orchestrated by Step Functions.
```

---

## 5. Deployment and Versioning

**Q:** How to deploy and manage Lambda versions?

**A:** Use versions and aliases to manage releases and traffic shifting. Use CI/CD pipelines (CodePipeline, CodeDeploy, or third-party tools) for automated deployments and canary/linear deployments.

Example:
```
Use alias "prod" pointing to version 12, perform gradual traffic shift to version 13 with CodeDeploy to test new changes safely.
```

---

## 6. Security Best Practices

**Q:** How to secure Lambdas?

**A:** Grant least-privilege IAM roles per function, store secrets in AWS Secrets Manager or SSM Parameter Store, use VPC access for private resources, and enable encryption for environment variables when needed.

Example:
```
Function needs DB credentials: Lambda role can read secrets from Secrets Manager; do not hard-code secrets in environment variables.
```

---

## 7. Observability: Logging and Tracing

**Q:** How to monitor Lambda functions?

**A:** Use CloudWatch Logs for logs, CloudWatch Metrics for invocations/errors/duration, X-Ray for distributed tracing, and structured logging for easier analysis.

Example:
```
Enable X-Ray tracing and instrument key segments to trace a request from API Gateway -> Lambda -> RDS to find latency hotspots.
```

---

## 8. Cost Considerations

**Q:** How is Lambda billed and how to optimize cost?

**A:** Billed per 100ms based on memory and execution time and per-request fees. Optimize by reducing execution time, choosing appropriate memory size (memory increases CPU), and avoiding long-running synchronous work.

Example:
```
A function with heavy CPU-bound work may be cheaper to allocate more memory to reduce runtime, lowering total billed duration.
```

---

## 9. Integration Patterns

**Q:** Common serverless integration patterns?

**A:**
- Event-driven (S3/SNS/SQS -> Lambda)
- API backend (API Gateway -> Lambda)
- Stream processing (Kinesis/DynamoDB Streams -> Lambda)
- Orchestration (Step Functions orchestrate multiple Lambdas)

Example:
```
Order processing pipeline: API Gateway -> Lambda (validate) -> SNS -> downstream Lambdas handle payments, inventory, and notifications.
```

---

## 10. Common Interview Mistakes

**A:**
- Not handling retries and idempotency (important with SQS, SNS, and event sources)
- Leaving broad IAM permissions on Lambda roles
- Trying to run long, stateful processes better suited for containers

---

## Quick Checklist

```
✓ Use least-privilege IAM roles
✓ Keep functions stateless and idempotent
✓ Use X-Ray and CloudWatch for observability
✓ Consider Provisioned Concurrency for low-latency needs
✓ Store secrets in Secrets Manager or SSM
✓ Use Step Functions for complex workflows
```
