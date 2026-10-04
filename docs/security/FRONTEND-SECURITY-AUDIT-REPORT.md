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

- Severity: CRITICAL in development tooling only
- Area: npm supply chain
- Evidence: `npm audit --omit=dev` reports zero vulnerabilities. Full audit now reports two vulnerable `piscina` package nodes through `@angular/build@21.2.24`; both map to advisory `GHSA-67c8-pqhq-4rmx`, affecting `piscina >=5.0.0 <5.3.2`. The safe `http-cache-semantics` high advisory was remediated to 4.3.0. The remaining forced fix downgrades Angular build tooling to 19.2.27.
- Impact: developer/CI tooling risk; no production runtime package is in the affected dependency graph.
- Fix: no unsafe forced downgrade; retain the current Angular-major alignment and track the supported build-toolchain patch.
- Validation: production dependency audit is clean; `npm ci` succeeds from the lockfile; production build passes; `piscina` is absent from the production dependency tree and emitted browser bundles.
- Status: ACCEPTED NON-PRODUCTION RISK

## Review result

No confirmed frontend Critical/High runtime vulnerability was found. Auth uses the stateful Sanctum session with credentials and XSRF handling, without browser bearer/JWT storage. Angular interpolation/property binding is used for untrusted text; no `innerHTML`, sanitizer bypass, eval, dynamic template, message-passing, iframe, or service-worker sink was found. Signed verification URLs are backend-provided, short-lived, opened with `noopener,noreferrer`, and are not persisted.

Local validation covered the deterministic EC-015 reporting suite (4/4), the EC-016 admin/document browser suite (6/6), focused auth/interceptor tests, production build, and dependency reproducibility. Deployment-owned CSP, HSTS, TLS, CDN cache, production CORS, and public source-map hosting still require deployment validation.
