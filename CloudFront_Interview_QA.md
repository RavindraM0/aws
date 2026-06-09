# Amazon CloudFront Interview Questions & Answers
## Important & Frequently Asked Questions with Real-World Examples

---

## 1. What is CloudFront?

**A:** CloudFront is AWS’s global content delivery network (CDN). It caches content at edge locations to reduce latency and offload origin servers.

**Real-World Example:**
```
Serve website assets (HTML/CSS/JS, images, video) from nearest edge location to reduce page load time for global users.
```

---

## 2. Distribution types: Web vs RTMP (historical)

**A:** Web distributions are used for HTTP/S content. RTMP was historically used for streaming but is not commonly used today; modern streaming uses CloudFront combined with AWS Media Services.

**Example:**
```
Host a single-page app via a Web distribution pointing to an S3 origin or an ALB.
```

---

## 3. Origins and Origin Groups

**Q:** What is an origin? When use origin groups?

**A:** An origin is where CloudFront fetches content (S3, ALB, EC2, or a custom origin). Origin Groups provide primary/secondary origin failover for availability.

**Example:**
```
Primary origin is S3; secondary is an origin in another region (or a fallback origin) to increase resilience.
```

---

## 4. Cache behaviors and path patterns

**Q:** How do behaviors work?

**A:** Behaviors map path patterns to cache settings, allowed HTTP methods, TTLs, and origin selection. Use multiple behaviors to tune caching per asset type.

**Example:**
```
/api/* -> forward all headers to origin (no caching)
/static/* -> cache aggressively with long TTLs
```

---

## 5. Cache keys & cache policies

**Q:** How to control what CloudFront caches on?

**A:** Use cache policies (which query strings, headers, cookies to include in cache key) and origin request policies (which values to forward to origin). Minimize cache key cardinality to increase cache hit ratio.

**Example:**
```
Avoid forwarding session cookies for static assets; only forward necessary headers for dynamic routes.
```

---

## 6. TTLs and invalidation

**Q:** TTL strategy and how to invalidate objects?

**A:** Use appropriate Min/Max/Default TTLs. Invalidations remove objects from edge caches (costs apply). Use versioned object names (cache-busting) for frequent updates to avoid repeated invalidations.

**Example:**
```
Deploy new frontend by uploading files with a content-hash in filenames (index.abc123.js) and update HTML to reference it — no invalidation needed.
```

---

## 7. Signed URLs and Signed Cookies

**Q:** How to restrict access to private content?

**A:** Use CloudFront signed URLs/cookies or Origin Access Control (OAC) with signed requests to S3. Signed URLs/cookies support time-limited access for paid content or downloads.

**Example:**
```
Generate a signed URL for a paid video allowing access for 1 hour.
```

---

## 8. Origin Access: OAI vs OAC

**Q:** How to protect S3 origins?

**A:** Use Origin Access Identity (OAI) or Origin Access Control (OAC) so S3 objects are not publicly accessible; CloudFront fetches via this identity. OAC is the newer, recommended approach for signing requests with AWS SigV4 to S3.

**Example:**
```
Block public access on the bucket and use OAC to let CloudFront read private content.
```

---

## 9. Security: HTTPS, WAF, and TLS

**Q:** Best security practices?

**A:** Enforce HTTPS via Viewer Protocol Policy, use custom TLS certificates via ACM (CloudFront requires ACM certs in us-east-1), integrate AWS WAF for web ACLs, and use strict TLS policies to disable old ciphers.

**Example:**
```
Protect application endpoints with WAF rules (rate limiting, SQLi/XSS protections) in front of CloudFront.
```

---

## 10. Lambda@Edge vs CloudFront Functions

**Q:** When to use which?

**A:** CloudFront Functions are lightweight, fast (millisecond) for simple manipulations at Viewer Request/Response. Lambda@Edge supports heavier compute, Node.js/Python runtimes and runs at viewer/origin phases with more capability.

**Example:**
```
Use CloudFront Functions for header rewrites and A/B testing; use Lambda@Edge for complex auth token verification or HTML transformations.
```

---

## 11. Logging, Metrics, and Monitoring

**Q:** How to monitor CloudFront?

**A:** Enable CloudFront access logs to S3, use CloudWatch metrics (Requests, 4xx/5xx, CacheHitRate, BytesDownloaded), and set alarms. Use real-user monitoring or synthetic checks for performance.

**Example:**
```
Alert when CacheHitRate drops below threshold to investigate origin misconfiguration or high cardinality cache keys.
```

---

## 12. Price Class and Cost Controls

**Q:** How to reduce costs?

**A:** Use Price Classes to limit edge locations (e.g., Price Class 100 for US/Europe). Optimize cache hit ratio, avoid unnecessary invalidations, and minimize origin egress by caching more at the edge.

**Example:**
```
For a regional app, restrict to nearby edge locations via price class to save costs.
```

---

## 13. Compression and Performance

**Q:** How to improve performance?

**A:** Use gzip/Brotli compression at origin or let CloudFront compress objects. Cache compressed variants and set Vary headers properly.

**Example:**
```
Serve pre-compressed assets and ensure CloudFront caches the correct content-encoding.
```

---

## 14. Geo restrictions and edge location behavior

**Q:** How to restrict content by geography?

**A:** Use Geo Restriction to allow/deny content by country or use CloudFront Functions/Lambda@Edge for custom logic.

**Example:**
```
Block access from specific countries due to licensing with Geo Restrictions.
```

---

## 15. Common Interview Mistakes

- Over-forwarding headers/cookies causing cache misses.
- Relying heavily on invalidations instead of versioned assets.
- Not protecting S3 origins (leaving bucket public).
- Misunderstanding viewer cert location requirement (ACM in us-east-1).
- Confusing Lambda@Edge and CloudFront Functions capabilities.

---

## Quick Checklist

```
✓ Use cache policies and origin request policies to reduce cardinality
✓ Prefer versioned filenames over frequent invalidations
✓ Protect S3 with OAC/OAI and Block Public Access
✓ Use WAF + HTTPS + strict TLS policies
✓ Monitor CacheHitRate and origin errors in CloudWatch
✓ Choose CloudFront Functions for simple edge logic, Lambda@Edge for complex needs
```

---
