# Frontend Security Audit Report

EC-017-FE reviewed the Angular application at `f3bcca1908780b179ed0439d3306ad86ce1f0db5` against the EC-001–EC-016 browser security boundary.

## Findings

### FE-SEC-001 — 419 mutation replay

- Severity: MEDIUM
- Area: HTTP error handling / CSRF recovery
- Evidence: the interceptor retried every API request after a 419, including POST/PATCH/DELETE mutations.
- Impact: a mutation could be submitted twice after a CSRF/session-expiry response.
- Fix: retry only idempotent GET/HEAD/OPTIONS requests; mutations now surface the controlled 419 error.
- Validation: interceptor regression tests cover safe retry and mutation non-replay.
- Status: CLOSED

### FE-SEC-002 — stale authentication response after logout

- Severity: MEDIUM
- Area: session state isolation
- Evidence: an in-flight `/me` response could set the old user after logout had cleared state.
- Impact: stale authenticated UI could briefly reappear during a logout/user-switch race.
- Fix: authentication state carries a generation value; stale initialization success/failure responses are ignored.
- Validation: auth-service regression test covers logout before the in-flight `/me` response resolves.
- Status: CLOSED

### FE-SEC-003 — development dependency audit findings

- Severity: HIGH/CRITICAL in development tooling only
- Area: npm supply chain
- Evidence: `npm audit --omit=dev` reports zero vulnerabilities. Full audit reports 14 findings (12 high, 2 critical) through Angular CLI/build and their registry/signing tooling; the suggested forced fixes are breaking framework downgrades.
- Impact: developer/CI tooling risk; no production runtime package is in the affected dependency graph.
- Fix: no unsafe forced downgrade; keep the issue tracked for a compatible Angular toolchain patch and CI dependency review.
- Validation: production dependency audit is clean and the production build passes.
- Status: NON-BLOCKING FOLLOW-UP

## Review result

No confirmed frontend Critical/High runtime vulnerability was found. Auth uses the stateful Sanctum session with credentials and XSRF handling, without browser bearer/JWT storage. Angular interpolation/property binding is used for untrusted text; no `innerHTML`, sanitizer bypass, eval, dynamic template, message-passing, iframe, or service-worker sink was found. Signed verification URLs are backend-provided, short-lived, opened with `noopener,noreferrer`, and are not persisted.

Local validation covered lint, 67 unit tests, production build, focused auth/interceptor tests, and the EC-016 admin/document browser suite (6/6). The existing EC-015 reporting suite was attempted but could not enter its browser assertions because `EC15_REPORTER_*` fixture credentials were not present; it is retained as a test-infrastructure follow-up rather than treated as product evidence. Deployment-owned CSP, HSTS, TLS, CDN cache, production CORS, and public source-map hosting still require deployment validation.
