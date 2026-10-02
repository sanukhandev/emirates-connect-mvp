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
- `notifications`: in-app notification payload/read state (can be deferred if needed).

`reels` stores `author_type`/`author_id`, `created_by_user_id`, plain-text caption, controlled processing status, optional trusted media metadata, private source storage metadata, public playback/thumbnail metadata, and publication timestamps. Source and playback paths are internal only; soft deletion preserves the record while removing managed assets. Composite indexes cover chronological feed ordering and author/status listings.

Indexes must cover feed ordering, author lookups, business membership, comment parent/post, moderation state and verification state. Use FK constraints where practical and explicit delete behavior.
