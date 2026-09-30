# Engineering Workflow

Branches: `main` protected; short-lived `feature/EC-###-description`, `fix/...`, `chore/...` in each repository.

A cross-stack feature normally lands in this order: API contract -> backend -> Angular/Flutter consumers -> parent submodule pointers/docs. Child PRs can be developed concurrently against the agreed contract.

## Parent pointer update
```bash
git submodule update --init --recursive
cd backend && git checkout <approved-sha> && cd ..
cd frontend && git checkout <approved-sha> && cd ..
cd mobile && git checkout <approved-sha> && cd ..
git add backend frontend mobile
git commit -m "chore: update application pointers"
```

Never point parent `main` to an unpublished child commit.

## EC-001 local foundation checks

Backend:

```bash
cd backend
composer install
cp .env.example .env
php artisan key:generate
php artisan migrate
php artisan test
```

Frontend:

```bash
cd frontend
npm ci
npm run build
npm test -- --watch=false
```

The mobile repository is not yet scaffolded; Flutter setup and validation remain a follow-up to EC-001.


## UI change checklist
For Angular/Flutter UI work, PRs must state whether new tokens or primitives were introduced. Prefer extending the parent design system over feature-specific visual rules. Angular UI must use Tailwind CSS and Ubuntu; screenshots at desktop/tablet/mobile breakpoints are recommended for significant views.
