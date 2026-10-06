# Frontend Performance Remediation Plan — EC-018-FE

## Open, deployment-scale items

1. Capture real production Web Vitals (LCP, CLS, INP), API latency and geographic network behavior.
2. Validate CDN/image resizing, Brotli/gzip, HTTP/2 or HTTP/3 and media delivery for production hosting.
3. Revisit feed virtualization only if long-depth browsing produces unacceptable DOM or interaction cost.

These are deployment or scale follow-ups, not confirmed local application defects.

## Closed in this review

- Removed duplicate notification unread-count request caused by page/service overlap.
- Retained cursor pagination, stale-response protection, lazy routes, media metadata preload and object-URL cleanup.
