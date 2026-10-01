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
Use Laravel Sanctum personal access bearer tokens for both Angular and Flutter clients. Tokens are revocable, named `api-session`, and sent only over HTTPS outside local development; clients must keep them in platform-appropriate secure storage. This API does not use Sanctum's stateful SPA cookie flow, so bearer requests do not rely on CSRF cookies. Authorization remains policy-based server-side.

## Media pipeline
Client requests upload intent -> API validates -> signed object-storage upload -> client completes upload -> API queues validation/transcode -> worker generates optimized renditions/thumbnail -> media becomes `ready` -> CDN serves rendition. Do not proxy large videos through PHP in production.

## Scalability path
Start modular monolith. Scale API/workers horizontally; move cache/queue to managed Redis; use read replicas/search service only after metrics justify them. Avoid premature microservices.


## Presentation architecture
The shared parent design system defines brand tokens and UI principles. Angular implements these through Tailwind CSS and reusable UI primitives; Flutter implements equivalent semantics through ThemeData/shared widgets. Visual tokens are shared conceptually but application code remains inside each submodule.
