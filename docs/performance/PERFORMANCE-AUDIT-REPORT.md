# Backend Performance Audit — EC-018

## Scope

Reviewed the EC-001–EC-017 backend hot paths on the local MariaDB-backed application: feed, search, reels, notifications, verification, moderation, admin lists, pagination, storage boundaries, cache/queue/session configuration, and rate-limit storage.

## Findings

### PERF-001 — Verification queue filesort

- Severity: **MEDIUM**
- Evidence: `WHERE status = 'pending' ORDER BY submitted_at DESC, id DESC` used `verification_requests_status_index` with `Using filesort` over 228 rows.
- Fix: replaced the single-column index with `verification_queue_order_index (status, submitted_at, id)`.
- After: `type=range`, selected key `verification_queue_order_index`, `rows=228`, no `Using filesort`.
- Status: **FIXED**.

### PERF-002 — Admin user/business ordering

- Severity: LOW
- Evidence: current plans scan 1,131 users and 342 businesses and use filesort for the unfiltered created-date ordering.
- Status: **ACCEPTABLE FOR MVP**; admin pagination is bounded at 20/50 and the tables are operational rather than public hot paths. Revisit composite ordering indexes when production cardinality and latency justify their write cost.

### PERF-003 — Search leading-wildcard scans

- Severity: LOW
- Evidence: contains search uses `%term%` across multiple fields and is not a B-tree prefix lookup; the representative `all` search plan scanned the 1,131-row users branch and 342-row businesses branch, then sorted the 1,440-row derived union.
- Status: **NON-BLOCKING FOLLOW-UP**; the current MVP safely binds and escapes search input. Move to full-text/search infrastructure only after measured volume or latency requires it.

## Query-plan summary

| Path | Plan | Assessment |
|---|---|---|
| Moderation pending | `range`, `reports_status_created_at_id_index`, 597 rows, no filesort | GOOD |
| Moderation pending + post | same composite index, 597 rows, no filesort | GOOD |
| Moderation pending + spam | same composite index, 597 rows, no filesort | GOOD |
| Verification pending | `range`, `verification_queue_order_index`, 228 rows, no filesort | GOOD |
| Feed posts | `range`, `posts_feed_order_index`, 467 rows | GOOD |
| Reel feed | `range`, `reels_feed_order_index`, 164 rows | GOOD |
| Search `q=ali` | users/businesses table scans; derived union 1,440 rows and filesort | ACCEPTABLE MVP |
| Notifications list | recipient ordering index, `type=ref` | GOOD |
| Notifications unread | recipient/read index, `Using index` | GOOD |
| Admin users | bounded scan/filesort | ACCEPTABLE MVP |
| Admin businesses | bounded scan/filesort | ACCEPTABLE MVP |

## Query-count and pagination review

- Mixed verification loading has a bounded regression test (`<12` queries) and uses controlled polymorphic eager loading.
- Reports, notifications, feed and reels are bounded and paginated.
- Feed, reels and notifications use cursor pagination with stable timestamp/id ordering.
- Search and admin lists use bounded page pagination; admin page size is capped at 50.
- No public unbounded collection was identified.

## Local baseline

These are development-server measurements only, not production capacity claims. At concurrency 20:

| Endpoint | Requests | Success | Req/s | p50 | p95 | p99 | Max |
|---|---:|---:|---:|---:|---:|---:|---:|
| `/api/v1/health` | 200 | 200 | 3.09 | 3383 ms | 6687 ms | 8763 ms | 9129 ms |
| `/api/v1/reels?per_page=20` | 200 | 200 | 2.59 | 4118 ms | 8235 ms | 11090 ms | 11485 ms |
| `/api/v1/search?q=ali&per_page=20` | 50 | 50 | 2.49 | 2294 ms | 4194 ms | 4734 ms | 4734 ms |

The single-process local server is the limiting factor; these timings require a real deployment load test before SLO decisions.
