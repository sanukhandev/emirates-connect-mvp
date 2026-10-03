# EC-017 Security Control Matrix

| Control | Implementation/evidence | Result |
| --- | --- | --- |
| Authentication | Sanctum stateful SPA session; mobile token endpoint is separate | PASS |
| CSRF | Sanctum CSRF cookie and stateful middleware for browser sessions | PASS |
| Session/logout | Login regenerates session; logout invalidates web session or revokes current PAT | PASS |
| Account suspension | `active.account` blocks protected access | PASS |
| Authorization | Policies, explicit ownership checks, `active.account`, and `system.admin` | PASS |
| Admin isolation | All `/api/v1/admin/*` routes require system admin; business roles are denied | PASS |
| IDOR | Resource-scoped policies/queries; notification, verification document, report, and moderation tests | PASS |
| Polymorphic safety | Enforced morph map aliases: user, business, post, comment, reel | PASS |
| Mass assignment | Deliberate fillable/hidden fields and validated explicit assignments | PASS |
| Input validation | FormRequests for writes, bounded text/pagination, enum allow-lists | PASS |
| SQL injection | Bound parameters; controlled raw SQL only for fixed search expressions | PASS |
| XSS boundary | User text remains JSON data; no server-side HTML trust path | PASS |
| Uploads | MIME/size validation, generated paths, private sensitive disks, SVG excluded | PASS |
| Path traversal | Client filenames are metadata only; generated storage paths and safe download names | PASS |
| Signed documents | Short-lived signed route plus active system-admin session, private disk, no-store | PASS |
| Rate limiting | Login, registration, password recovery, email verification, search, reports, reels, verification submissions, admin mutations/documents | PASS |
| Error handling | API JSON 401/403/422/429; debug must be disabled in production | PASS |
| Headers | Central middleware adds nosniff, referrer, permissions, frame protection; HSTS only secure production | PASS |
| CORS | Explicit configured origin with credentials; methods/headers allow-listed | PASS |
| Secrets | `.env` and credentials are ignored; no production key in `.env.example` | PASS |
| Logging/audit | Admin verification/moderation actions audited; signed URLs and tokens are not application-logged | PASS |
| Dependencies | `composer audit` reports no advisories | PASS |

Production infrastructure items such as HTTPS, proxy trust, Redis TLS/auth, object-storage policy, backup protection, and log access require deployment validation.
