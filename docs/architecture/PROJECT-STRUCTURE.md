# Project Structure

```text
emirates-connect/                 # parent orchestration repo
├── AGENTS.md
├── README.md
├── .gitmodules
├── backend/                      # git submodule: Laravel + MySQL
├── frontend/                     # git submodule: Angular + Tailwind CSS
├── mobile/                       # git submodule: Flutter
├── docs/
│   ├── product/MVP-SCOPE.md
│   ├── architecture/
│   │   ├── ARCHITECTURE.md
│   │   ├── PROJECT-STRUCTURE.md
│   │   └── DATA-MODEL.md
│   ├── design/
│   │   ├── DESIGN-SYSTEM.md
│   │   └── UI-PRINCIPLES.md
│   ├── api/openapi.yaml
│   ├── security/SECURITY.md
│   ├── deployment/DEPLOYMENT.md
│   └── development/WORKFLOW.md
└── .github/workflows/
    ├── validate-parent.yml
    ├── deploy-dev.yml
    └── deploy-production.yml
```

Recommended child roots:
```text
backend/
├── app/{Actions,Domain,Http,Models,Policies,Services}
├── database/{factories,migrations,seeders}
├── routes/api.php
└── tests/{Feature,Unit}

frontend/
└── src/
    ├── app/
    │   ├── core/                 # auth, API, guards, interceptors, config
    │   ├── shared/
    │   │   ├── ui/              # Tailwind-backed reusable UI primitives
    │   │   ├── models/
    │   │   └── utils/
    │   └── features/
    │       ├── auth/
    │       ├── onboarding/
    │       ├── feed/
    │       ├── profile/
    │       ├── businesses/
    │       ├── verification/
    │       ├── discussions/
    │       ├── reels/
    │       └── moderation/
    ├── styles.css                # Tailwind entry + global tokens
    └── index.html                # Ubuntu font/application metadata

mobile/
└── lib/
    ├── core/                     # API, auth, config, theme
    ├── shared/                   # shared widgets/models/utilities
    └── features/                 # auth, feed, profile, business, reels, etc.
```

## Ownership rule
- Parent: shared standards, contracts, docs, workflows and submodule pointers.
- Backend submodule: API/business/data implementation.
- Frontend submodule: Angular product UI and Tailwind implementation.
- Mobile submodule: Flutter application.
