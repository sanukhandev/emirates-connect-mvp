# Frontend Security Remediation Plan

## Open non-blocking items

1. Track a compatible Angular CLI/build-toolchain update for the `npm audit` findings in development-only dependencies. Do not apply the audit tool's breaking downgrade automatically. Re-run both full and `--omit=dev` audits after the supported patch is available.
2. Validate Angular-host deployment controls before production: HTTPS/TLS, HSTS, CSP, Referrer-Policy, Permissions-Policy, frame protection, explicit API CORS, CDN cache rules, and private source-map handling.

## Closed in EC-017-FE

- Prevented automatic replay of unsafe mutation requests after HTTP 419.
- Prevented stale `/me` responses from restoring a logged-out user.
- Retained signed verification-document privacy and opener isolation.
