# Logical Data Model

Core tables/entities:
- `users`: identity/auth status and lifecycle.
- `profiles`: public professional profile fields.
- `verification_requests`: subject, type, status, evidence references, reviewer/audit metadata.
- `businesses`: public business page.
- `business_members`: business-user role (`owner`, `admin`, optional `editor`).
- `posts`: polymorphic author (`user` or `business`), body, status, timestamps.
- `media`: owner/uploader, object key, MIME, type, processing state, dimensions/duration, renditions.
- `post_media`: ordered attachment mapping.
- `comments`: post, author, nullable `parent_id`, body, status.
- `reels`: author, media, caption, processing/publishing/moderation status.
- `reports`: reporter, reportable target, reason, status.
- `blocks`: blocker/blocked user unique pair.
- `audit_logs`: privileged/security-relevant actions.
- `notifications`: recipient, controlled type, safe actor/subject morph references, minimal navigation data and nullable `read_at` state.

`reels` stores `author_type`/`author_id`, `created_by_user_id`, plain-text caption, controlled processing status, optional trusted media metadata, private source storage metadata, public playback/thumbnail metadata, and publication timestamps. Source and playback paths are internal only; soft deletion preserves the record while removing managed assets. Composite indexes cover chronological feed ordering and author/status listings.

Indexes must cover feed ordering, author lookups, business membership, comment parent/post, moderation state and verification state. Use FK constraints where practical and explicit delete behavior.

Notification queries use `(recipient_user_id, created_at, id)` for stable listing and `(recipient_user_id, read_at, created_at)` for unread filtering/counts. A nullable unique `dedupe_key` prevents duplicate event delivery without deleting notification history when subjects disappear.
