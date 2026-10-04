# Frontend Security Remediation Plan

## Open non-blocking items

1. Track a compatible Angular CLI/build-toolchain update for the remaining `piscina` advisory in development-only dependencies. The related `http-cache-semantics` advisory is fixed at 4.3.0. Do not apply the audit tool's breaking Angular 19 downgrade automatically.
2. Validate Angular-host deployment controls before production: HTTPS/TLS, HSTS, CSP, Referrer-Policy, Permissions-Policy, frame protection, explicit API CORS, CDN cache rules, and private source-map handling.

## Closed in EC-017-FE

- Prevented automatic replay of unsafe mutation requests after HTTP 419.
- Prevented stale `/me` responses from restoring a logged-out user.
- Retained signed verification-document privacy and opener isolation.
- Replaced EC-015 environment credentials with deterministic runtime Eloquent fixtures; reporting suite passes 4/4.
