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

## Feed
EC-007 Phase 1 exposes an authenticated global chronological feed over the existing `posts` model. The query filters published, non-deleted posts with non-null `published_at`, then excludes suspended/disabled user authors and inactive/suspended business authors. Results are ordered by `published_at DESC, id DESC` and returned through cursor pagination; no follow graph, ranking score, or feed materialization is introduced. Future follow and ranking work can extend this query layer without creating a second content model.

## Comments and replies
Comments use one polymorphic `comments` table with `author_type` values `user` or `business`. A comment belongs to a post and may have one `parent_id`; only top-level comments may receive replies, so maximum nesting depth is one. `created_by` records the authenticated human actor when a business publishes a comment. Public listings return visible top-level comments with replies ordered by `created_at ASC, id ASC`; deleted parents and their replies are omitted. Current business membership, not historical authorship, controls business comment edits and deletes.

## Reactions
Reactions use one `reactions` table with `user_id` as the authenticated human actor and a polymorphic `reactable` target aliased as `post` or `comment`. A database unique constraint permits one active reaction per user and target; `PUT` is idempotent and switches the existing type, while `DELETE` removes it. Posts, comments, and replies share stable summary output with total counts and `current_user`; businesses are never reaction actors. Mutations require an active account and a publicly interactable target, and summaries are aggregate-loaded into existing post/comment resources without changing chronological feed ranking.

## Verification
Verification uses shared infrastructure with distinct User and Business subjects:

```text
User / Business
       |
VerificationRequest
       |-- VerificationDocument (private storage)
       `-- VerificationAuditLog (append-only)
```

The canonical subject state is `not_submitted -> pending -> approved|rejected`; rejected subjects may submit a new historical request, while approved subjects may not resubmit. Only one pending request is allowed per subject, enforced transactionally under a subject lock. User submissions always bind to the authenticated user. Business submissions require current owner/admin membership; business admin is not system admin. Public resources expose only `is_verified`; management/admin resources expose controlled status and review data. Documents remain private and are accessed by system admins only through short-lived signed URLs.

## Follow network
EC-010 adds one directional follows table: an authenticated human User is always the follower and the polymorphic target is either a User or Business (user/business aliases). A database unique constraint permits one edge per follower/target. User and business follow mutations are idempotent, self-follow is rejected, and businesses are targets only. Public follower/following lists are paginated and exclude hidden accounts/targets. User resources expose follower/following counts; business resources expose follower counts. The graph is intentionally not connected to feed ordering or personalization yet.

## Media pipeline
Client requests upload intent -> API validates -> signed object-storage upload -> client completes upload -> API queues validation/transcode -> worker generates optimized renditions/thumbnail -> media becomes `ready` -> CDN serves rendition. Do not proxy large videos through PHP in production.

## Scalability path
Start modular monolith. Scale API/workers horizontally; move cache/queue to managed Redis; use read replicas/search service only after metrics justify them. Avoid premature microservices.


## Presentation architecture
The shared parent design system defines brand tokens and UI principles. Angular implements these through Tailwind CSS and reusable UI primitives; Flutter implements equivalent semantics through ThemeData/shared widgets. Visual tokens are shared conceptually but application code remains inside each submodule.
