# AWS CloudWatch Interview Questions & Answers
## Important & Frequently Asked Questions with Real-World Examples

---

## 1. What is Amazon CloudWatch?

**Q:** What is CloudWatch?

**A:** CloudWatch is a monitoring and observability service for AWS resources and applications. It collects metrics, logs, and events and can trigger alarms and automated actions.

Real-World Example:
```
Monitor EC2 CPU, RDS connections, Lambda errors, and trigger an SNS alert to on-call when thresholds are breached.
```

---

## 2. Metrics, Logs, and Events

**Q:** What are the main CloudWatch components?

**A:**
- **Metrics:** Numerical data (CPUUtilization, NetworkIn)
- **Logs:** Application and system logs (CloudWatch Logs)
- **Events / EventBridge:** System events and scheduling rules

Example:
```
Use CloudWatch Metrics + Alarms for CPU spikes and CloudWatch Logs Insights to query error logs across instances.
```

---

## 3. Alarms and Actions

**Q:** How do alarms work and what can they do?

**A:** Alarms evaluate metrics against thresholds and can trigger actions like SNS notifications, Auto Scaling policies, or Systems Manager automation.

Example:
```
Alarm: CPU > 80% for 5 min -> SNS -> PagerDuty; or trigger Auto Scaling to add instances
```

---

## 4. CloudWatch Logs and Insights

**Q:** How to query logs effectively?

**A:** Use CloudWatch Logs Insights with structured logs (JSON) and fields to run powerful queries, aggregate results, and visualize trends.

Example:
```
Query error count per service over the last 24 hours and group by error type to prioritize fixes.
```

---

## 5. Custom Metrics and High Cardinality

**Q:** When to use custom metrics and how to avoid costs?

**A:** Publish application-specific metrics (e.g., order processed count) via PutMetricData. Avoid high-cardinality dimensions (like per-user IDs) as they increase costs and reduce query performance.

Example:
```
Track orders-per-minute as a custom metric, but avoid tagging metrics per customer ID.
```

---

## 6. Dashboards and Visualization

**Q:** How to build useful dashboards?

**A:** Combine metrics and log-based metrics on dashboards, focus on key SLOs/SLIs, use annotations for deployments, and create role-specific dashboards (ops, dev, business KPIs).

Example:
```
Ops dashboard: CPU, memory, latencies, error rates. Business dashboard: orders per minute, failed payments.
```

---

## 7. Contributor Insights & Anomaly Detection

**Q:** What advanced features help detect issues?

**A:** Contributor Insights identifies top contributors to noisy logs/metrics. Anomaly Detection uses ML to surface metric anomalies based on historical data.

Example:
```
Anomaly detection identifies sudden latency increase outside normal seasonal patterns and creates an alarm.
```

---

## 8. Log Retention and Cost Controls

**Q:** How to manage log retention and cost?

**A:** Set retention policies, archive old logs to S3 with lifecycle rules, and use log filters to route only required logs. Use metric filters sparingly to avoid extra charges.

Example:
```
Keep 90 days in CloudWatch Logs for production; archive older logs to S3 Glacier for compliance.
```

---

## 9. Integration with Other AWS Services

**Q:** How does CloudWatch integrate with other AWS services?

**A:** CloudWatch alarms can trigger SNS, SQS, Lambda, Auto Scaling, and Systems Manager. CloudWatch Events/EventBridge can route events to targets for automation.

Example:
```
Use EventBridge to schedule a Lambda that runs nightly reports, or trigger remediation Lambda when a security alarm fires.
```

---

## 10. Common Interview Mistakes

**A:**
- Relying only on default metrics (e.g., not monitoring memory)
- Not setting proper retention or cost controls for logs
- Using high-cardinality custom metrics unnecessarily
- Not instrumenting application-level metrics (SLIs)

---

## Quick Checklist

```
✓ Monitor system & application metrics
✓ Create alarms for SLO breaches
✓ Use CloudWatch Logs Insights for log analytics
✓ Archive old logs to S3 to control costs
✓ Avoid high-cardinality metrics
✓ Use dashboards for SLO/SLA visibility
```
