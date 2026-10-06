# Frontend Performance Audit — EC-018-FE

Date: 2026-10-06  
Scope: Angular frontend at `cc2096053dbd59b6a0f723cb153a9a82ab08fd2b`.

## Findings

### FE-PERF-001 — duplicate notification unread-count request

- Severity: MEDIUM
- Area: Notifications
- Evidence: `NotificationService` refreshes the count on authenticated-user initialization; `NotificationsPageComponent` also refreshed it whenever query parameters emitted. This caused two identical unread-count requests on initial notification navigation.
- Fix: removed the page-level refresh. The service remains responsible for auth-boundary refreshes and read-state updates.
- Validation: unit suite remains green; the service’s existing auth-boundary tests cover authoritative count refresh and user-switch reset.
- Status: FIXED

No other confirmed severe frontend performance defect was found in this review.

## Tooling dependency note

`source-map-js` was updated from 1.2.1 to 1.2.2 in the lockfile. It is development/build tooling only and is not emitted into browser runtime bundles. The remaining `piscina` critical advisory is transitive through `@angular/build` 21.2.24, is development/build-only, and requires a future compatible Angular build-tool update; `npm audit --omit=dev` remains clean.

## Build evidence

Production build (`ng build --stats-json`):

| Output | Raw | Estimated transfer |
|---|---:|---:|
| Initial total | 334.59 kB | 87.11 kB |
| Main | 6.65 kB | 1.75 kB |
| Styles | 41.44 kB | 6.40 kB |
| Largest lazy chunk | 46.07 kB | 9.36 kB |
| Admin lazy chunk | 36.58 kB | 7.62 kB |

Production budgets are present (initial warning/error 500 kB/1 MB; component-style warning/error 4 kB/8 kB). Output hashing is enabled and no production source maps were emitted.

## Structural review

- All application routes, including admin, verification, moderation, search, reels and notifications, use lazy `loadComponent` routes.
- No preloading strategy is configured; large private/admin features are therefore not eagerly prefetched.
- Feed, reels, search and notifications use cursor/request-version protection and stable identity tracking. Feed/reels use `IntersectionObserver` for load-more.
- Reels use `preload="metadata"`; local preview object URLs are revoked.
- No service worker, polling loop, runtime bearer storage, or broad third-party runtime library was found.
- Deterministic performance E2E now provisions fixtures through Laravel Tinker and completes real Angular/Sanctum login; feed and notification scenarios pass 2/2.

## Browser performance evidence

- Feed: 60 posts, 3 pages, one initial request and one request per load-more, cursor progression present, no duplicate post IDs.
- Notifications: 45 notifications, 3 pages, one initial request and two load-more requests, no duplicate IDs, one unread-count request during stable SPA login/navigation.
- The original registration bootstrap failure was classified as environment/configuration: the required local services were not listening, leaving the browser on `/register`; no registration or backend contract defect was found.
- Focused performance suite: 2 total, 2 passed, 0 failed, 0 skipped. Existing delayed-search regression: 1 passed.

## Follow-ups

Production Web Vitals, CDN/image transformation, compression, geographic latency and long-depth feed virtualization require deployment-scale validation and remain outside this local review.
