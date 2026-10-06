# EC-019 Automated UAT Matrix

Run: `ec19uat-1791279240183` (local, 2026-10-06)

| Journey | Persona | Preconditions | Automated result | Manual follow-up | Defect | Status |
|---|---|---|---|---|---|---|
| Guest discovery and mutation protection | Guest | No session | Public search/reels/reporting surfaces and protected-route behavior covered | Visual/public-content review | None | PASS |
| Authentication and logout | User | Runtime fixture credentials | Real Sanctum login/logout and session behavior covered | Browser-specific presentation | None | PASS |
| Profile and media | User | Completed profile fixture | Profile media lifecycle and persistence covered | Real-world upload feel | None | PASS |
| Business ownership and roles | Owner/Admin/Editor/Non-member | Business fixture | Creation, management permissions, membership and cross-role denial covered | Responsive visual review | None | PASS |
| Posts and feed | User/Business | Active user/business fixtures | Create/edit/delete, mixed authors, cursor pagination and dedupe covered | Media UX | None | PASS |
| Comments and replies | Users | Post fixture | Root comments, one-level replies, edit/delete and pagination covered | Copy/visual review | None | PASS |
| Reactions | Users | Post/comment fixtures | Add/switch/remove and guest/rollback behavior covered | None | None | PASS |
| Follow | Users | Public user/business fixtures | Follow/unfollow, directionality, idempotency and guest behavior covered | None | None | PASS |
| Search and discovery | Guest/User | Deterministic EC12 fixture | Filters, pagination, URL state and stale-response regression covered | Cross-browser presentation | None | PASS |
| Reels | User/Business | Runtime media fixture | Create/play/edit/list/delete and business authoring covered | Real-device playback feel | None | PASS |
| Notifications | Users | Notification-triggering fixtures | Delivery, unread/read, mark-all, privacy and responsive behavior covered | Visual/copy review | None | PASS |
| Verification | User/Business/System Admin | Runtime admin and profile fixtures | Submission, file validation, approval/rejection, privacy and signed document flow covered | Stakeholder workflow review | None | PASS |
| Reporting | User | Deterministic EC-017 fixtures | All supported targets, duplicate/own-target, XSS/plain text, rate-limit UX covered | Copy review | None | PASS |
| Moderation and admin console | System Admin | Admin fixtures | Dashboard, verification, reports, audit, user/business administration and isolation covered | Visual polish | None | PASS |
| Cross-user isolation | Multiple | Separate runtime accounts | Logout/user switching and admin/notification isolation covered by existing suites | Manual multi-browser smoke | None | PASS |
| Security/performance regression | User/Admin | Existing EC-017/018 suites | 419 no-replay, stale auth, request dedupe, cursor pagination, lazy chunks and source-map policy retained | Production-only checks | None | PASS |

## Aggregate

- Browser tests: 67 passed, 0 failed, 0 skipped after deterministic fixture setup and the test-only media assertion correction.
- Backend regression: 99 passed, 689 assertions.
- Frontend unit regression: 67 passed across 25 files.
- Lint, build and runtime dependency audit: PASS.

