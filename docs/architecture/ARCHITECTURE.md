# System Architecture

```text
Angular + Tailwind Web -\
                      > HTTPS /api/v1 -> Laravel API -> MySQL
Flutter Mobile ------/                    |   |   |
                                          |   |   +-> Queue workers
                                          |   +-----> Redis/cache + queue backend
                                          +---------> Object Storage -> CDN
                                                     |-> video processing/transcoding

Admin/moderation: protected Laravel API + dedicated Angular admin routes/app.
```

## Bounded domains
Identity, Profiles, Verification, Businesses, Publishing, Discussions, Reels/Media, Moderation, Notifications, Analytics.

## API design
JSON REST, `/api/v1`, cursor pagination for feeds, consistent error envelope, idempotency for selected create/upload completion calls, optimistic concurrency where edits can collide.

## Authentication
Use Laravel Sanctum's hybrid model: Angular uses stateful first-party SPA authentication with a Laravel session cookie and CSRF protection; Flutter uses revocable personal access bearer tokens named from the device. Shared protected routes use `auth:sanctum`, and authorization remains policy-based server-side.

## Identity and profiles
The `users` table is the authentication/account identity boundary. The one-to-one `profiles` table is the professional/public identity boundary: display name, headline, biography, work context, controlled industry/emirate values and managed media metadata. Profile ownership is enforced through `/me` routes; public profile resources do not expose account email or security fields.

## Business pages and membership roles
Businesses are professional pages, not authentication identities. Human users authenticate and operate pages through the `business_members` relationship:

```text
User <-> BusinessMember <-> Business
```

Each created business receives one owner membership in the same transaction. Additional memberships use the controlled `owner`, `admin` and `editor` roles; owner/admin capabilities are enforced by `BusinessPolicy`, and member routes are scoped to the requested business to prevent cross-business IDOR. Business slugs are stable after creation. Business identity uses the existing controlled industry and emirate enums, while logo and cover media use the Laravel Storage abstraction.

## Posts and publishing
Posts use one polymorphic `posts` table with `author_type` values `user` or `business`. A user remains the authenticated human actor (`created_by`); a business is the publishing identity when a member publishes on its behalf. `post_media` stores image metadata and object-storage paths for up to four JPEG, PNG or WebP images per post. Policies enforce user ownership and active business membership for owner/admin/editor publishing and management. Public endpoints expose published posts only; drafts are limited to their author or authorized business members.

## Media pipeline
Client requests upload intent -> API validates -> signed object-storage upload -> client completes upload -> API queues validation/transcode -> worker generates optimized renditions/thumbnail -> media becomes `ready` -> CDN serves rendition. Do not proxy large videos through PHP in production.

## Scalability path
Start modular monolith. Scale API/workers horizontally; move cache/queue to managed Redis; use read replicas/search service only after metrics justify them. Avoid premature microservices.


## Presentation architecture
The shared parent design system defines brand tokens and UI principles. Angular implements these through Tailwind CSS and reusable UI primitives; Flutter implements equivalent semantics through ThemeData/shared widgets. Visual tokens are shared conceptually but application code remains inside each submodule.
