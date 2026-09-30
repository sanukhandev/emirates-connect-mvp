# Emirates Connect — AI Engineering Instructions

## Mission
Build Emirates Connect as a UAE-focused professional network for business owners, founders, entrepreneurs and verified businesses. Phase 1 is an MVP; avoid unrelated LinkedIn-scale features.

## Repository topology
This parent repository is the source of truth for cross-project contracts, architecture, product/design documentation and deployment orchestration. Application code lives in Git submodules:
- `backend/` — Laravel + MySQL REST API
- `frontend/` — Angular + Tailwind CSS web application
- `mobile/` — Flutter iOS/Android application

Never commit application source into the parent repository. Parent commits update submodule pointers only after the child commit is pushed.

## Phase 1 scope
1. User registration/login/onboarding and professional profile.
2. User verification workflow.
3. User posts with media.
4. Comments and threaded replies.
5. Business pages, ownership/admin roles, and business posts/latest updates.
6. Reels/Shorts-style vertical video feed.
7. Feed, profile, business-page and basic discovery surfaces.
8. Moderation/reporting, blocking and baseline admin controls required to safely operate the above.

Out of scope unless explicitly approved: jobs, marketplace, paid ads, subscriptions, direct messaging, groups, advanced recommendation ML, live streaming.

## Brand & UI rules — mandatory
The design language is modern, simple, professional and Bento-inspired. Web and mobile must feel like one product.

### Brand tokens
- Primary: `rgb(148 112 248)` / `#9470F8`.
- Primary is used for primary actions, active states, selected navigation, focus accents, verified/brand highlights and key data visualization accents.
- Neutral canvas: warm off-white rather than pure white.
- Surfaces: off-white, white and restrained cool/warm greys.
- Text: high-contrast charcoal/dark grey; avoid pure black unless necessary for accessibility.
- Typography: **Ubuntu only** (Google Font). Do not introduce Inter, Roboto, Poppins, system-brand fonts or other product fonts.

### Bento design principles
- Compose dashboards, profiles, discovery and business pages from modular cards in responsive Bento grids.
- Prefer clear information hierarchy over decorative complexity.
- Use generous whitespace, consistent card radii, subtle borders and restrained shadows.
- Avoid glassmorphism-heavy, neon, skeuomorphic or overly gradient-driven interfaces.
- Large cards may span multiple columns only when the content priority justifies it.
- Cards must collapse predictably on tablet/mobile; never preserve desktop spans at the cost of readability.
- Reuse the same surface, radius, spacing and interaction tokens across features.
- Motion must be short, subtle and functional; respect reduced-motion preferences.

### Angular styling mandate
- Tailwind CSS is the required styling system for the Angular application.
- Centralize brand tokens as CSS custom properties and expose them through Tailwind theme utilities.
- Prefer Tailwind utilities and reusable Angular UI primitives over one-off component CSS.
- Component CSS is allowed only for cases Tailwind cannot express cleanly or for encapsulated complex behavior; document the reason.
- Do not hard-code repeated hex/rgb values in templates. Use semantic classes/tokens such as `bg-brand-primary`, `text-content-primary`, `bg-surface-card`.
- Build reusable primitives for Button, Card, Avatar, Badge, Input, Textarea, Modal/Sheet, Tabs, Dropdown, Skeleton, EmptyState and BentoGrid/BentoCard.
- All states require hover/focus/disabled/loading/error behavior.
- Responsive design is mobile-first.
- Meet WCAG 2.2 AA contrast and keyboard/focus requirements.

### Flutter styling mandate
- Flutter must mirror the same semantic token system through `ThemeData`, `ColorScheme`, `TextTheme`, spacing/radius constants and reusable widgets.
- Ubuntu must be bundled/configured using the approved Google Fonts approach for Flutter.
- Do not create a visually separate mobile brand.

Detailed rules: `docs/design/DESIGN-SYSTEM.md` and `docs/design/UI-PRINCIPLES.md`.

## Architecture rules
- Laravel is the authoritative business/data layer. Angular and Flutter never connect directly to MySQL.
- Expose versioned REST APIs under `/api/v1`.
- API contracts are shared in `docs/api/openapi.yaml`; update the contract before/with breaking implementation changes.
- Use UUID/ULID public identifiers; never expose sequential database IDs as a security boundary.
- Store media in object storage/CDN, not MySQL. Store metadata and ownership in MySQL.
- Use queues for video processing, notifications, emails and expensive async work.
- Cache feed/read-heavy resources with Redis when introduced; correctness must not depend on cache.
- All timestamps are UTC in persistence/API; clients localize display.
- Soft delete user-generated content where audit/moderation requires it.

## Backend conventions
- Thin controllers; business logic in Actions/Services; authorization in Policies; validation in Form Requests.
- Eloquent models must define fillable/guarded behavior deliberately.
- Prevent N+1 queries; paginate all collections.
- Use transactions for multi-record invariants.
- API Resources own response serialization.
- Feature tests are mandatory for auth, authorization, ownership, verification, posting, comments, business pages and moderation.

## Angular conventions
- Feature-first structure with standalone components where appropriate.
- Tailwind CSS is mandatory for product UI styling.
- Typed API clients/models; no `any` in production code without documented reason.
- Route guards are UX only; backend authorization remains authoritative.
- Keep tokens out of localStorage when secure cookie-based auth is used.
- Lazy-load feature routes and optimize image/video delivery.
- Shared UI primitives live under `src/app/shared/ui` and must follow the parent design system.
- Feature components should consume semantic design tokens rather than invent feature-specific visual systems.
- Prefer accessible semantic HTML before adding ARIA.

## Flutter conventions
- Feature-first modules with presentation/domain/data separation.
- One HTTP/API abstraction and one auth/session abstraction.
- Secure credentials/tokens via platform secure storage where tokens are required.
- No business authorization logic trusted solely to the client.
- Video feed must manage lifecycle, preloading and disposal to avoid memory leaks.
- Shared widgets must use the Emirates Connect theme/token layer.

## Security & privacy
- Apply OWASP ASVS/API Security principles.
- Rate-limit auth, verification, posting, commenting, reporting and media endpoints.
- Validate MIME type, size and extension; malware-scan uploads where infrastructure permits.
- Signed upload/download URLs for private or pending media.
- Enforce RBAC/ownership server-side on every mutation.
- Passwords use Laravel's supported adaptive hashing; never encrypt passwords.
- Secrets only via environment/secret manager; never commit `.env`, credentials or signing keys.
- Log security/audit events without logging passwords, tokens, OTPs or unnecessary PII.
- Implement account deletion/export hooks and retention rules before production.

## Git/submodule workflow
1. Work in the relevant child repository/branch.
2. Test/lint/build child repository.
3. Commit and push child changes.
4. In parent, update only the child submodule pointer plus contract/docs/workflows if needed.
5. Parent PR must identify child commit SHAs and compatibility implications.

Do not silently modify more than one submodule for a single task unless the contract requires coordinated changes. Never reset, force-push, rewrite history, or update a submodule pointer to an unpushed commit.

## Definition of done
A task is done only when acceptance criteria pass, authorization/negative paths are tested, lint/build/tests pass, API docs are current, migrations are reversible, no secrets are added, relevant parent documentation/pointers are updated, and UI work conforms to the Emirates Connect design system.
