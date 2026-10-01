# Security Baseline

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

## Privacy
Collect only fields required for the business-network use case. Separate public profile/business data from private account/verification data. Define retention/deletion/export processes and avoid placing PII in logs, analytics events, filenames or object keys.
