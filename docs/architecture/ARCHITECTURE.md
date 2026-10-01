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

## Media pipeline
Client requests upload intent -> API validates -> signed object-storage upload -> client completes upload -> API queues validation/transcode -> worker generates optimized renditions/thumbnail -> media becomes `ready` -> CDN serves rendition. Do not proxy large videos through PHP in production.

## Scalability path
Start modular monolith. Scale API/workers horizontally; move cache/queue to managed Redis; use read replicas/search service only after metrics justify them. Avoid premature microservices.


## Presentation architecture
The shared parent design system defines brand tokens and UI principles. Angular implements these through Tailwind CSS and reusable UI primitives; Flutter implements equivalent semantics through ThemeData/shared widgets. Visual tokens are shared conceptually but application code remains inside each submodule.
