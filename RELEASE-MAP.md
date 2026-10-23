# Release to Deployment Map

## Overview
This document maps every standardized semantic release tag to its exact commit SHA, creation date, deployment record, and release contents. It serves as the primary operational source of truth that answers the two essential questions during an incident:
1. **What version is running right now in production?**
2. **What known-good version do we roll back to?**

---

## Production Release Map

| Version (tag) | Commit (short hash) | Date | What Shipped | Deployment Status | Rollback Target |
| :--- | :--- | :--- | :--- | :--- | :--- |
| **`v1.1.1`** | `82535d9` | 2026-06-06 | Refactored dummy checkout service entrypoint (`run()`), added service configuration constants (`SERVICE_NAME`, `SERVICE_VERSION`), and updated console output. | **Current Production** (Deployed) | `v1.1.0` (`86989b6`) |
| **`v1.1.0`** | `86989b6` | 2026-06-06 | Added structured release notes tracking, historical changelog records, and operational audit logging documentation. | **Previous Known-Good Release** (Staging/Hot-Standby) | `v1.0.0` (`ff1fb9a`) |
| **`v1.0.0`** | `ff1fb9a` | 2026-06-06 | Initial checkout service scaffold, baseline application execution logic, and core repository scaffolding. | **Baseline Initial Release** (Archived) | Initial commit (`4febcc6`) |

---

## Legacy Tag to Semantic Release Reconciliation

To resolve the historical tagging confusion documented in `TAG-AUDIT.md`, the table below maps legacy ad-hoc tags to the new semantic version standard:

| Legacy Tag | Commit SHA | Old Tag Type | Flaw / Anti-Pattern | Replaced By Standard Tag |
| :--- | :--- | :--- | :--- | :--- |
| `v2-final-FINAL`, `patch-new` | `82535d9` | Lightweight | Unprofessional name, duplicated aliases on same commit | **`v1.1.1`** |
| `1.5.0` | `86989b6` | Annotated | Missing `v` prefix, broke lexicographical sorting | **`v1.1.0`** |
| `latest-good` | `da6b2ed` | Annotated | Mutable status name pointing to obsolete commit | Reconciled into commit history |
| `version-1.0`, `release_2` | `28bd110` | Mixed | Inconsistent naming and duplicate tag on same commit | Reconciled into documentation history |
| `v1.4.2`, `stable-build` | `ff1fb9a` | Mixed | Inverted semver chronology, duplicate tag on same commit | **`v1.0.0`** |

---

## Proving Rollback Traceability

### Step-by-Step Incident Rollback Procedure

When a critical incident or regression occurs in production on release `v1.1.1`, the incident response team executes the following deterministic procedure:

#### Step 1: Query the Sortable Release History
Run the semantic version sort command to inspect the latest deployed release and immediately identify the preceding stable release:
```bash
git tag --sort=-v:refname
```
**Output:**
```
v1.1.1
v1.1.0
v1.0.0
```
*The sortable release hierarchy instantly reveals that `v1.1.1` is the current release and `v1.1.0` is the immediate, prior known-good version.*

#### Step 2: Verify Release Metadata and Annotation
Inspect the target tag to verify the author, release notes, and commit SHA:
```bash
git show v1.1.0
```

#### Step 3: Checkout and Roll Back to the Last Known-Good Tag
Check out the verified tag and trigger redeployment:
```bash
git checkout v1.1.0              # roll back to the last known-good tag, then redeploy
```

#### Step 4: Verify System State
Verify that the working tree is safely detached at the exact known-good commit:
```bash
git log -1 --oneline
# 86989b6 (tag: v1.1.0) docs: add old release notes and changelog
```

---

### Why Rollback Traceability is Now Precise and Reproducible

Under the new Semantic Versioning convention, release tags are strictly formatted as `vMAJOR.MINOR.PATCH`, annotated with immutable release notes, and mapped directly to linear commit hashes. This enables incident responders to run `git tag --sort=-v:refname` and deterministically identify the exact prior release (`v1.1.0`) in milliseconds, without ambiguity or reliance on human memory. 

In contrast, the legacy repository was an unnavigable graveyard of floating names (`latest-good`), inverted versions (`v1.4.2` placed before `version-1.0`), and informal aliases (`v2-final-FINAL`), where picking a rollback candidate was dangerous guesswork that caused multiple production outages and 502 errors.
