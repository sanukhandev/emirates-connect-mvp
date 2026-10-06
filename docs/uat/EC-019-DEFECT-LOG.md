# EC-019 Automated UAT Defect Log

## EC19-001 — Legacy browser-registration fixture was rate-limited

- Severity: MAJOR test-blocking harness defect; not a product defect.
- Journey: Business, follow, post, reaction and verification setup.
- Steps: Run the existing suite without pre-provisioned accounts; submit registration repeatedly.
- Expected: Fixture reaches `/onboarding`.
- Actual: Registration remained at `/register` with `Too many attempts. Please try again shortly.`
- Root cause: Shared browser-registration fixture depended on the auth rate limiter and was unsuitable for bulk UAT setup.
- Fix: Replaced fixture setup with runtime Laravel Tinker/Eloquent user/profile provisioning followed by real Angular/Sanctum UI login.
- Regression: Business permissions, follows, posts, reactions and verification suites passed.
- Status: CLOSED.

## EC19-002 — Search fixture variables were not provisioned

- Severity: MAJOR test-blocking harness defect; not a product defect.
- Journey: Search filters and pagination.
- Expected: Deterministic EC12 records available to the suite.
- Actual: Tests stopped when `EC12_SEARCH_RUN_ID` was absent.
- Fix: Provisioned deterministic runtime business fixtures with a unique run marker and exported the marker for the suite.
- Regression: Both affected search tests passed; the complete search suite passed.
- Status: CLOSED.

## EC19-003 — Profile media test required URL changes for identical image bytes

- Severity: MINOR test assertion defect; not a product defect.
- Journey: Profile media replacement.
- Expected: Replacement upload succeeds and remains rendered after reload.
- Actual: Test required the URL string to differ even though both fixture images contained identical bytes and the API legitimately reused the same path.
- Fix: Removed the false URL-inequality assertion; retained upload response, render, reload and delete assertions.
- Regression: Profile media test passed.
- Status: CLOSED.

No open BLOCKER, CRITICAL or MAJOR product defects remain.

