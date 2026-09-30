# Emirates Connect Platform

Parent orchestration repository for the Emirates Connect Phase 1 MVP — a UAE-focused professional network for business owners and entrepreneurs.

## Stack
- Backend: Laravel + MySQL
- Web: Angular + Tailwind CSS
- Mobile: Flutter
- API authentication foundation: Laravel Sanctum
- Cache and queue foundation: Redis
- API base path: `/api/v1`
- Typography: Ubuntu (Google Font) only
- Brand primary: `rgb(148 112 248)` / `#9470F8`
- UI direction: modern, simple, professional Bento design
- Media: object storage + CDN
- Platform services as needed: Redis, queues/workers, FFmpeg/transcoding service

## Submodules
```text
backend/   -> Laravel API repository
frontend/  -> Angular + Tailwind web repository
mobile/    -> Flutter mobile repository
```

## Parent responsibility
The parent repository owns cross-project architecture, product scope, API contracts, security rules, the shared design system, deployment workflows and exact child repository pointers.

## Brand snapshot
```text
Primary        #9470F8  rgb(148 112 248)
Canvas         #F8F7FA  warm off-white
Surface        #FFFFFF
Surface muted  #F1F0F4
Border         #E4E1EA
Text primary   #27242D
Text secondary #6F6A78
Font           Ubuntu
```

These are baseline semantic tokens. Product code should consume tokens, not duplicate raw values throughout templates/widgets. See `docs/design/DESIGN-SYSTEM.md`.

## Bootstrap
```bash
git clone --recurse-submodules <PARENT_REPO_URL>
cd emirates-connect
git submodule update --init --recursive
```

Add submodules initially with:
```bash
git submodule add <BACKEND_REPO_URL> backend
git submodule add <FRONTEND_REPO_URL> frontend
git submodule add <MOBILE_REPO_URL> mobile
git commit -m "chore: add application submodules"
```

See `docs/` for product scope, architecture, API contract, data model, UI/brand system, security, development and deployment guidance.

## EC-001 foundation status

- Backend: Laravel 12 application root with Sanctum, MySQL/Redis environment configuration, Redis queue configuration, CORS and `GET /api/v1/health`.
- Frontend: Angular 21 application root with Tailwind CSS 4, Ubuntu, semantic Emirates Connect tokens, a responsive Bento shell and a typed health API client.
- Mobile: Flutter repository remains a placeholder until the mobile toolchain is available; no mobile application source is committed yet.

## Local development

Backend requirements: PHP 8.2+, Composer, MySQL and Redis.

```bash
cd backend
composer install
cp .env.example .env
php artisan key:generate
php artisan migrate
php artisan serve
```

Run the Redis-backed worker separately:

```bash
cd backend
php artisan queue:work redis
```

Frontend requirements: Node.js compatible with the locked Angular toolchain.

```bash
cd frontend
npm ci
npm start
```

The frontend uses `http://localhost:8000/api/v1` in development. Flutter setup is pending the mobile toolchain installation.
