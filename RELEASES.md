# Formal Release Notes

This document provides structured release notes for each semantic version tag in the repository. Release notes bridge the gap between Git commit hashes and human-intelligible traceability, documenting what changed, why, and the exact rollback target if an operational failure occurs.

---

## Release v1.1.1

- **Release Tag:** `v1.1.1`
- **Release Date:** 2026-06-06
- **Target Commit:** `82535d9`
- **Commit Title:** `feat: add dummy checkout service`
- **Rollback Target:** **`v1.1.0`** (Commit `86989b6`)

### Summary of Changes:
Refactored the core execution structure of the dummy checkout service application to adhere to standardized entrypoint naming and exposed explicit service metadata constants.

### Grouped Changes:
- **Added:**
  - Added `SERVICE_NAME` constant (`'checkout-service'`) to `src/app.js` for standardized service identification.
  - Added `SERVICE_VERSION` constant (`'0.0.0-placeholder'`) to provide runtime version visibility in application startup logs.
- **Changed:**
  - Refactored primary execution function from `runCheckout()` to `run()` to align with microservice bootstrap conventions.
  - Updated console output to include formatted service name and version strings during startup.
- **Fixed:**
  - Standardized application startup logging format to prevent parsing errors in log aggregation pipelines.

---

## Release v1.1.0

- **Release Tag:** `v1.1.0`
- **Release Date:** 2026-06-06
- **Target Commit:** `86989b6`
- **Commit Title:** `docs: add old release notes and changelog`
- **Rollback Target:** **`v1.0.0`** (Commit `ff1fb9a`)

### Summary of Changes:
Introduced comprehensive release history documentation, audit changelog specifications, and legacy release note references to begin formalizing deployment tracking.

### Grouped Changes:
- **Added:**
  - Added legacy release note reference documentation (`docs/release-notes-old.md`) detailing historical release descriptions.
  - Added historical changelog ledger (`CHANGELOG.md`) documenting past deployment milestones.
- **Changed:**
  - Updated repository documentation hierarchy to separate operational incident logs from development documentation.
- **Fixed:**
  - Clarified documentation ambiguity between staging promotion records and production release notes.

---

## Release v1.0.0

- **Release Tag:** `v1.0.0`
- **Release Date:** 2026-06-06
- **Target Commit:** `ff1fb9a`
- **Commit Title:** `docs: scaffold assignment repository`
- **Rollback Target:** None (Initial baseline release; emergency fallback is repository initial commit `4febcc6` or maintenance splash page)

### Summary of Changes:
Initial production baseline release establishing repository scaffolding, core checkout service implementation, and baseline configuration.

### Grouped Changes:
- **Added:**
  - Added baseline `src/app.js` checkout service logic and placeholder execution harness.
  - Added `src/README.md` defining service boundary and scope.
  - Created initial repository structure including `CHANGELOG.md`, `README.md`, and deployment history specifications.
- **Changed:**
  - Transitioned repository from empty initialization commit (`4febcc6`) to fully runnable microservice structure.
- **Fixed:**
  - N/A (Baseline initial release).
