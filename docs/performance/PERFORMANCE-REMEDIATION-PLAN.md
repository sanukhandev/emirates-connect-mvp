# Backend Performance Remediation Plan

## Closed in EC-018

- EC-015 moderation queue/index EXPLAIN validation: **CLOSED — current index/query acceptable**.
- Verification queue filesort: **CLOSED — fixed with `verification_queue_order_index (status, submitted_at, id)`**.

## Non-blocking follow-ups

- Run authenticated multi-worker load tests against feed, search, notifications and admin queues in a deployment-like environment.
- Reassess admin user/business ordering indexes after production cardinality is known.
- Move contains-search workloads to full-text/search infrastructure if measured latency warrants it.
- Move reel processing and notification persistence to dedicated Redis workers when request volume makes synchronous work material.
- Validate Redis/shared cache, shared sessions, object storage/CDN, PHP-FPM sizing, OPcache and slow-query monitoring during deployment.

