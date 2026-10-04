# Security Baseline

The EC-017 review evidence is maintained in [SECURITY-AUDIT-REPORT.md](SECURITY-AUDIT-REPORT.md), [SECURITY-CONTROL-MATRIX.md](SECURITY-CONTROL-MATRIX.md), [SECURITY-REMEDIATION-PLAN.md](SECURITY-REMEDIATION-PLAN.md), and [PRODUCTION-SECURITY-GATE.md](PRODUCTION-SECURITY-GATE.md).

The frontend-specific evidence is maintained in [FRONTEND-SECURITY-AUDIT-REPORT.md](FRONTEND-SECURITY-AUDIT-REPORT.md), [FRONTEND-SECURITY-CONTROL-MATRIX.md](FRONTEND-SECURITY-CONTROL-MATRIX.md), [FRONTEND-SECURITY-REMEDIATION-PLAN.md](FRONTEND-SECURITY-REMEDIATION-PLAN.md), and [FRONTEND-PRODUCTION-SECURITY-GATE.md](FRONTEND-PRODUCTION-SECURITY-GATE.md).

## Required before MVP production
- TLS only; HSTS at edge when domain is stable.
- Secure authentication, revocation and password-reset flow.
- Email/phone verification as product requires; OTPs short-lived, hashed where persisted, rate-limited.
- Policy authorization for all resource mutations and business-page role actions.
- Sanctum stateful SPA sessions with CSRF for Angular, and hashed personal access bearer tokens for Flutter; CORS remains an explicit credentialed allow-list.
- Strict request validation and output encoding.
- Upload size/MIME/signature validation; quarantine/process video before publication.
- Rate limits and abuse controls for auth, comments, posts, reels, verification and reports.
- Secrets in CI/secret manager only.
- Database encryption at rest via infrastructure plus field-level protection for especially sensitive verification evidence metadata as appropriate.
- Backups with restore test; production least privilege.
- Audit admin/reviewer actions.
- Account blocking/reporting/content takedown.
- Dependency scanning and SAST in CI.
- Profile mutations are ownership-scoped through `/me`; public resources exclude email and authentication data.
- Profile media accepts only validated JPEG, PNG or WebP uploads, uses generated Storage filenames, and protects internal storage paths from API serialization.
- Profile URLs are server-validated for safe HTTP(S) schemes, with LinkedIn restricted to LinkedIn hosts.
- Business pages are operated through authenticated user memberships; owner/admin/editor role boundaries and last-owner protection are enforced server-side.
- Business member mutations are scoped to the parent business to prevent cross-business IDOR and role escalation; owners cannot be removed through the generic member endpoint.
- Business logos and covers accept only validated JPEG, PNG or WebP uploads, use generated Storage filenames, clean up managed replacements, and do not expose internal paths.
- Public business resources expose active business presentation data only; creator, membership and internal moderation fields remain private. Inactive and suspended pages return 404 publicly.
- Posts prevent user impersonation by resolving `user` authorship from the authenticated account and require active owner/admin/editor membership for `business` authorship.
- Post bodies are plain text, media is limited to validated JPEG/PNG/WebP images (8 MB each, four per post), and generated storage paths are never serialized.
- Drafts and soft-deleted posts are excluded from public resources; post media deletion is scoped through its parent post to prevent cross-post IDOR.
- The authenticated Phase 1 feed is limited to published, non-deleted posts from active user accounts and active businesses; drafts, suspended/disabled users, and inactive/suspended businesses are excluded before serialization.
- Feed cursors use deterministic `published_at`/`id` ordering with bounded page sizes, preventing offset-style page shifting and unbounded collection reads.
- Comments derive user authorship from the authenticated account, require current active business membership for business authorship and mutation, reject replies to replies, and scope replies to the parent comment's post.
- Comment bodies are trimmed plain text with a 2,000-character limit; author identity, post identity and `created_by` are immutable and private fields/raw morph classes are excluded from resources.
- Comments are disabled for drafts, deleted posts and posts hidden by suspended/disabled users or inactive/suspended businesses. Soft-deleted comments and replies are excluded from public listings.
- Reactions are limited to active authenticated human users; business identities cannot react. A database unique `(user_id, reactable_type, reactable_id)` constraint prevents duplicate rows, and enum validation blocks arbitrary reaction types.
- Reaction targets reuse post/comment visibility checks, so drafts, deleted targets, hidden authors, inactive businesses, deleted comments and hidden replies return unavailable responses. Resources expose only aggregate counts and the current user's type, never reactor identities or internal fields.
- Follows use a database unique constraint across follower user, target type and target id, reject self-follow, and accept only active authenticated human users as actors. User and business targets must be publicly visible (active); hidden targets return unavailable responses, while follower/following resources expose only public fields and filter hidden follower accounts. Businesses cannot act as followers and follow edges are never used to personalize the chronological feed.
- Verification requests use explicit `user`/`business` morph aliases, bind user submissions to the authenticated account, and authorize business submissions through current owner/admin membership; business roles do not grant system-admin review access.
- Verification documents are stored on a dedicated private disk under generated unpredictable paths. Uploads use a PDF/JPEG/PNG/WebP allow-list, a 10 MB per-file limit and five-file request limit; original filenames are metadata only. Normal resources omit storage paths and URLs. System admins receive only five-minute signed document access, scoped to the parent request, with access audit entries.
- Verification review is limited to system admins and uses transactional `pending -> approved|rejected` transitions. Duplicate pending requests, invalid final-state transitions, cross-request document access and non-admin review are blocked. Public user/business resources expose only the derived `is_verified` badge; rejection reasons, documents, reviewer data and audit history remain private.
- Search is public but rate-limited to 60 requests per minute. Search inputs are length-bounded, validated against the canonical industry/emirate/type values, parameter-bound and LIKE-wildcard escaped. Search branches reuse active user/business visibility conditions and return only compact public fields; email, account status, admin state, verification workflow state and documents are never searchable or serialized. The EC-016 admin verification fix uses controlled polymorphic loading for mixed user/business subjects.
- Reels accept one MP4 video up to 100 MB per mutation, store the source under a generated key on a private disk, and expose only a playback URL after publication. Source paths, disks, processing errors and internal author/creator fields are excluded from public resources. User authorship is bound to the authenticated user; business authorship requires current active owner/admin/editor membership. Hidden users and inactive businesses hide their published reels, mutation endpoints use a named 20-per-hour limiter, and delete removes stored source/playback/thumbnail assets where present.
- Notifications are private to the authenticated human recipient; list, unread-count, mark-read and mark-all queries are recipient-scoped and cross-user IDs return 404. Payloads contain only controlled types, compact public actor metadata and minimal IDs. Self-actions are suppressed, event dedupe keys prevent repeated follow/reaction/review delivery, hidden actors resolve to null, and system-admin identity, reviewer details, rejection reasons and verification documents are never serialized.

## EC-017 production requirements

- Registration is source-rate-limited; verification submissions, report creation, admin mutations and signed verification-document downloads have dedicated throttles.
- API responses receive baseline `nosniff`, referrer, permissions and frame-protection headers. HSTS is emitted only for secure production requests.
- Production deployment must set `APP_ENV=production`, `APP_DEBUG=false`, HTTPS, secure HttpOnly cookies, explicit CORS origins, intentional trusted-proxy settings, and private storage policies. Local development values are not production approval.

## Privacy
Collect only fields required for the business-network use case. Separate public profile/business data from private account/verification data. Define retention/deletion/export processes and avoid placing PII in logs, analytics events, filenames or object keys.
# Reporting and moderation controls

- Reporters are session-derived; client input cannot select a reporter or raw morph class.
- Target aliases are allow-listed and resolved through canonical public visibility checks.
- One unresolved report per reporter/target is allowed; reporting is limited to 10 requests per hour per user/IP.
- Only active system admins can review reports. Business administrators are not platform moderators.

## Admin Console controls

- EC-016 admin routes require `auth:sanctum`, `active.account` and `system.admin`; business roles do not cross this boundary.
- Admin user/business resources omit password hashes, remember tokens, Sanctum tokens, private media paths and verification document paths. No impersonation, password administration or generic database mutation endpoint exists.
- Direct admin suspension uses explicit reason validation, protects all system-admin accounts, and writes an append-only moderation audit entry.
- Verification and moderation audit endpoints are system-admin-only and serialize safe aliases/compact identities. Public resources continue to expose only derived verification badges and never reports, reviewer identity or audit history.
- Report details and resolutions are plain text with bounded lengths.
- Reporter resources omit reviewer identity, internal resolution and audit history; public resources expose no report data.
- Moderation actions are target-specific, transactionally locked, and recorded in append-only audit logs.
- Reviewer identity and private verification/storage data are not exposed to reporters or public resources.
