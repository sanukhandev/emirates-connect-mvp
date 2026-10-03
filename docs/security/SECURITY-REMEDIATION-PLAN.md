# EC-017 Remediation Plan

## Closed in EC-017-BE

1. Throttle public registration by source IP.
2. Throttle verification submissions.
3. Throttle system-admin mutations and signed verification document downloads.
4. Normalize verification download filenames before creating `Content-Disposition`.
5. Add centralized baseline security response headers, with production-only HSTS.
6. Restrict configured CORS methods and request headers while retaining the explicit credentialed frontend origin.

## Non-blocking deployment follow-ups

- Verify production HTTPS, `APP_DEBUG=false`, secure cookie settings, trusted proxy configuration, and HTTPS URL generation.
- Verify object-storage private/public bucket policies and worker isolation.
- Verify Redis authentication/TLS, database least privilege, protected backups, and log access controls.
- Retain the EC-015 moderation queue/index EXPLAIN work for EC-018 unless separately scheduled.
