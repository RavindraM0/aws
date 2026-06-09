# AWS S3 Interview Questions & Answers
## Important & Frequently Asked Questions with Real-World Examples

---

## 1. What is Amazon S3?

**Q:** What is S3?

**A:** Amazon Simple Storage Service (S3) is an object storage service that stores and retrieves any amount of data from anywhere. S3 stores objects in buckets and offers high durability, availability, and scalability.

**Real-World Example:**
```
Photo-sharing app:
- Users upload images → stored as objects in S3
- Each image has metadata (content-type, user-id)
- Frontend serves images via pre-signed URLs or CloudFront
- S3 provides durable storage and cheap delivery when paired with CDN
```

---

## 2. Buckets and Objects

**Q:** What's the difference between a bucket and an object?

**A:** A bucket is a container for storing objects. An object is the file + metadata stored in S3. Objects are identified by a key (path-like string) within a bucket.

**Real-World Example:**
```
Bucket: mycompany-user-data
Object key: avatars/2026/06/09/user12345.png
Object metadata: content-type=image/png, userId=12345
```

---

## 3. Storage Classes

**Q:** What are common S3 storage classes and use-cases?

**A:**
- **S3 Standard**: Frequent access, low latency
- **S3 Intelligent-Tiering**: Auto moves objects between tiers based on access
- **S3 Standard-IA**: Infrequent access, lower cost, retrieval fee
- **S3 One Zone-IA**: Single AZ, cheaper but no AZ redundancy
- **S3 Glacier** / **Glacier Deep Archive**: Long-term archival, retrieval latency minutes to hours

**Real-World Example:**
```
Media company:
- Active video edits: S3 Standard
- Completed projects kept for 1 year: Standard-IA
- Old archives for compliance: Glacier Deep Archive
- Use lifecycle rules to transition automatically
```

---

## 4. Versioning and Lifecycle Policies

**Q:** How do versioning and lifecycle policies help?

**A:**
- **Versioning**: Keeps multiple versions of an object to protect against accidental deletes/overwrites.
- **Lifecycle Policies**: Automatically transition objects between storage classes or expire them after a set time.

**Real-World Example:**
```
Corporate documents:
- Enable versioning to recover from accidental deletes
- Lifecycle rule: after 90 days → move to Standard-IA; after 365 days → Glacier
- Cost and data-protection balance
```

---

## 5. Security: Bucket Policies, ACLs, and IAM

**Q:** How do you secure S3 data?

**A:**
- Use **Bucket Policies** for fine-grained access control at bucket/object level
- Avoid ACLs; prefer bucket policies and IAM
- Enable **SSE-S3** or **SSE-KMS** for server-side encryption
- Use **S3 Block Public Access** to prevent accidental exposure
- Use IAM roles for applications (no long-term credentials)

**Real-World Example:**
```
Analytics pipeline:
- Ingest bucket: only writable by ingestion service role
- Processing role: read from ingest, write to processed-bucket
- Block public access on both buckets
- SSE-KMS for processed-bucket to meet compliance
```

---

## 6. Pre-signed URLs and CloudFront

**Q:** When to use pre-signed URLs vs CloudFront?

**A:**
- **Pre-signed URLs**: Grant short-term access to private objects directly from S3
- **CloudFront**: CDN for fast global delivery; can use origin access identity / OIDC or signed URLs/cookies for private content

**Real-World Example:**
```
Video streaming:
- Use CloudFront for global low-latency delivery
- Use signed CloudFront URLs for paid content
- Use S3 pre-signed URLs for temporary upload from client apps
```

---

## 7. Cross-Region Replication (CRR)

**Q:** What is CRR and when to use it?

**A:** CRR replicates objects across buckets in different regions for DR, compliance, or lower-latency access. It requires versioning enabled.

**Real-World Example:**
```
Global app:
- Primary region: us-east-1
- Replica region: eu-west-1 for European customers
- CRR ensures copies are available in EU for compliance and faster access
```

---

## 8. Event Notifications

**Q:** How can S3 notify other services of changes?

**A:** S3 can send event notifications to SNS, SQS, or Lambda on events like object created/deleted. Useful for automated processing.

**Real-World Example:**
```
Image processing flow:
- Upload to ingest-bucket triggers Lambda via S3 event
- Lambda generates thumbnails and stores them in processed-bucket
- SQS used for batching to handle high upload bursts
```

---

## 9. Data Consistency

**Q:** What are S3 consistency guarantees?

**A:** S3 provides read-after-write consistency for PUTs of new objects and eventual consistency for overwrite PUTs and deletes (historically), but modern S3 provides strong consistency for all operations.

**Real-World Example:**
```
After uploading a new file, your application can immediately read it without delay due to strong consistency
```

---

## 10. Cost Optimization

**Q:** How to reduce S3 costs?

**A:**
- Use lifecycle policies to move infrequently accessed data to cheaper tiers
- Use Intelligent-Tiering for unpredictable access patterns
- Delete unused objects and incomplete multipart uploads
- Use S3 analytics to find candidates for transition

**Real-World Example:**
```
Large logs bucket:
- Enable lifecycle: 30 days → Standard-IA, 180 days → Glacier
- Clean up logs older than 3 years
- Result: 60% cost reduction on storage bill
```

---

## 11. Common Interview Mistakes

**Q:** What do candidates often get wrong?

**A:**
- Exposing buckets publicly by misconfigured ACLs or policies
- Confusing object keys with directories
- Not using lifecycle policies or versioning for critical data
- Using S3 for low-latency database-like use-cases

---

## 12. Quick Checklist

```
✓ Enable Block Public Access unless public
✓ Enable versioning for critical buckets
✓ Use lifecycle rules to transition old data
✓ Prefer bucket policies and IAM over ACLs
✓ Use SSE-KMS for sensitive data
✓ Use CloudFront for global performance
✓ Monitor costs with S3 analytics and Cost Explorer
```

---

## Summary

S3 is simple to use but powerful. For interviews, focus on storage classes, security (bucket policies, encryption), lifecycle management, CRR, pre-signed URLs vs CloudFront, and event-driven architectures.
