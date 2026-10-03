# EC-017 Backend Security Audit

Date: 2026-10-03  
Scope: Laravel API and security-sensitive storage paths across EC-001–EC-016.

## Method

The review combined route and middleware inspection, targeted source searches, existing feature coverage, focused regression tests, dependency checks, and local HTTP/configuration smoke checks. No secrets or private fixture data are recorded here.

## Findings

| ID | Severity | Area | Finding | Fix/Status |
| --- | --- | --- | --- | --- |
| SEC-017-01 | MEDIUM | Abuse protection | Registration had no route throttle, allowing unbounded account-creation attempts from one source. | Fixed with `auth-register` (5/minute per IP); regression test added. Closed. |
| SEC-017-02 | MEDIUM | Admin abuse protection | Sensitive admin mutations and signed document access had no dedicated rate limit. | Fixed with `admin-mutations` (30/minute) and `admin-documents` (30/minute). Closed. |
| SEC-017-03 | LOW | Download response | Verification download filenames were derived from user metadata without an explicit safe-header normalization step. | Fixed with basename, character allow-list, and bounded filename generation. Closed. |

No confirmed CRITICAL or HIGH backend vulnerability was found. Existing EC-016 authentication handling, signed document authorization, cross-request document scoping, and private storage controls were retained and covered by regression tests.

## Reviewed controls

- Stateful Sanctum sessions use HttpOnly cookies and CSRF/XSRF conventions; browser responses do not add bearer tokens.
- API authentication failures render JSON 401 responses instead of redirecting to a missing login route.
- `active.account` and `system.admin` remain mandatory for admin APIs and signed verification downloads.
- Business roles do not grant platform-admin access.
- Explicit FormRequests, allow-listed morph aliases, generated upload paths, private verification/reel source disks, and API Resources prevent raw internal fields from being client-controlled or serialized.
- Search filters use bound parameters and controlled SQL fragments; no user-controlled sort column or direction was found.
- Sensitive admin actions and document downloads are throttled and existing audit/transactional controls remain in place.

## Deployment-only checks

Local development runs with debug enabled and non-secure cookies by design. Production must set `APP_ENV=production`, `APP_DEBUG=false`, HTTPS, secure HttpOnly cookies, explicit trusted frontend origins, trusted proxy configuration, protected storage, and least-privilege infrastructure credentials. These cannot be verified from this local checkout.
