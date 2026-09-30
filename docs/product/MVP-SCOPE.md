# Phase 1 MVP Scope

## Product objective
A trusted UAE business networking platform where entrepreneurs/business owners establish identity, represent businesses, publish updates and short videos, and engage in professional discussions.

## Epics
### EC-01 Identity & onboarding
Email/phone registration, login, password reset, profile, avatar, headline/title, bio, emirate, industry, skills/interests, onboarding completion.

### EC-02 Verification
Verification request, evidence/document metadata, review status (`unverified`, `pending`, `verified`, `rejected`), reviewer notes, audit trail and visible badge. Verification must not expose submitted evidence publicly.

### EC-03 Social publishing
Text/image/video posts, edit/delete, visibility state, feed pagination, reactions optional only if approved. Author can be a user or a business page where authorized.

### EC-04 Discussions
Comments and replies. Model as parent-child comments with bounded nesting for UI/abuse control. Author edit/delete and moderation state.

### EC-05 Business pages
Create business page, logo/cover, name, description, industry, website, contact/public location fields, owner/admin roles, verification status, business posts/latest updates.

### EC-06 Reels/Shorts
Vertical short-video publishing and feed. Upload -> processing -> ready/failed state; thumbnail; caption; duration; moderation status. MVP ordering may be chronological/recency-based; do not build ML recommendations yet.

### EC-07 Trust & safety
Report user/post/comment/business/reel, block user, admin moderation queue, content state, audit events and basic rate limits.

## MVP success signals
Activation (onboarding completion), verified-user ratio, weekly active publishers, post/comment engagement, business pages created, reel completion/view metrics, report resolution time.


## MVP experience direction
- Web uses Angular + Tailwind CSS with the shared Emirates Connect design system.
- Mobile uses Flutter with equivalent semantic theme tokens.
- Ubuntu is the only product font.
- Primary brand color is `#9470F8` / `rgb(148 112 248)`.
- Overall UI is modern, simple, professional and Bento-inspired.
- Feed readability, verified identity and business credibility take precedence over decorative effects.
- Responsive layouts must recompose rather than simply shrink desktop card grids.
