# Security Baseline

## Required before MVP production
- TLS only; HSTS at edge when domain is stable.
- Secure authentication, revocation and password-reset flow.
- Email/phone verification as product requires; OTPs short-lived, hashed where persisted, rate-limited.
- Policy authorization for all resource mutations and business-page role actions.
- Sanctum bearer-token authentication for Angular and Flutter; bearer API calls do not use cookie-auth CSRF, and CORS remains an explicit allow-list.
- Strict request validation and output encoding.
- Upload size/MIME/signature validation; quarantine/process video before publication.
- Rate limits and abuse controls for auth, comments, posts, reels, verification and reports.
- Secrets in CI/secret manager only.
- Database encryption at rest via infrastructure plus field-level protection for especially sensitive verification evidence metadata as appropriate.
- Backups with restore test; production least privilege.
- Audit admin/reviewer actions.
- Account blocking/reporting/content takedown.
- Dependency scanning and SAST in CI.

## Privacy
Collect only fields required for the business-network use case. Separate public profile/business data from private account/verification data. Define retention/deletion/export processes and avoid placing PII in logs, analytics events, filenames or object keys.
