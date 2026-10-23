# Engineering Release Versioning Policy & Tagging Convention

## Purpose
This document defines the formal versioning and release tagging standard for all engineering repositories. Compliance with this policy is mandatory for all code merged into the default production branch and deployed to staging or production environments. Adherence ensures unambiguous releases, automated tooling compatibility, and rapid, deterministic rollbacks during incidents.

---

## 1. Semantic Versioning Rules (SemVer 2.0.0)

Releases follow the standard `MAJOR.MINOR.PATCH` numerical format:

### Definitions:
- **MAJOR (`X.0.0`):** Incremented when breaking, backwards-incompatible API, architecture, database schema, or configuration changes are introduced. Consuming clients or dependent microservices must update their integrations.
- **MINOR (`X.Y.0`):** Incremented when new functionality or features are added in a backwards-compatible manner. Deprecated functionality may be flagged, but existing contracts remain unbroken.
- **PATCH (`X.Y.Z`):** Incremented for backwards-compatible bug fixes, security patches, performance enhancements, or non-breaking internal refactors.

### Concrete Increment Examples:

| Bump Type | Initial Version | New Version | Concrete Scenario |
| :--- | :--- | :--- | :--- |
| **MAJOR** | `v1.4.2` | `v2.0.0` | **Breaking Change:** The legacy REST endpoint `/api/v1/checkout` was removed and replaced with GraphQL `/api/v2/graphql`, breaking existing mobile and web client payloads. |
| **MINOR** | `v1.0.0` | `v1.1.0` | **Backwards-Compatible Feature:** Added support for Apple Pay and Google Pay payment methods via an optional `paymentGateway` parameter without modifying existing card payment flows. |
| **PATCH** | `v1.1.0` | `v1.1.1` | **Backwards-Compatible Bug Fix:** Resolved a race condition causing gateway timeout errors on checkout retries; API schema and response contracts were completely unchanged. |

---

## 2. Tag Naming Format

### The Standard Pattern:
All release tags MUST strictly follow the format:
```
vMAJOR.MINOR.PATCH
```

- **Prefix Requirement:** The tag MUST always begin with a lowercase `v`.
- **Numerical Digits:** `MAJOR`, `MINOR`, and `PATCH` MUST be non-negative integers without leading zeros.
- **Strict Prohibition:**
  - DO NOT use bare version strings (e.g., `1.5.0` is strictly forbidden).
  - DO NOT use verbose prefixes (e.g., `version-1.0` is strictly forbidden).
  - DO NOT use arbitrary integers or underscores (e.g., `release_2` is strictly forbidden).
  - DO NOT use subjective or status-based names (e.g., `latest-good`, `stable-build`, `test-tag`).
  - DO NOT append informal suffixes (e.g., `v2-final-FINAL` is strictly forbidden).

### Formal Regular Expression Validation:
```regex
^v(0|[1-9]\d*)\.(0|[1-9]\d*)\.(0|[1-9]\d*)$
```

### Approved Example:
- Standard Production Release: `v1.0.0`, `v1.1.0`, `v1.1.1`, `v2.0.0`

---

## 3. Annotated vs. Lightweight Tags

### Team Standard:
**The team STRICTLY uses ANNOTATED TAGS (`git tag -a`) for all production and pre-production releases. Lightweight tags are strictly forbidden for releases.**

### Rationale:
1. **Immutable Git Object with Audit Provenance:**
   An annotated tag is created as a full Git object in `.git/objects/`. It encapsulates:
   - The tagger's legal name and verified email address (`tagger.name`, `tagger.email`).
   - An immutable timestamp of when the release was created.
   - An explicit release summary message detailing what shipped.
   - Cryptographic signing support (`-s` using GPG or SSH keys) to guarantee authenticity.
2. **Elimination of "Ghost" Releases:**
   Lightweight tags are merely floating pointers pointing directly to a commit object (identical to a branch head). They contain zero metadata, cannot be verified, and are easily moved or overwritten inadvertently.
3. **Tooling & Release Notes Compatibility:**
   Deployment pipelines, GitHub Releases, and release automation tools require the message payload of annotated tags to automatically generate deployment changelogs and audit reports.

### Exact Git Commands:

#### Creating an Annotated Release Tag:
```bash
# Standard Annotated Tag:
git tag -a v1.1.0 -m "Release 1.1.0: Add payment token caching and timeout remediation"

# Cryptographically Signed Annotated Tag (Recommended for Production):
git tag -s v1.1.0 -m "Release 1.1.0: Add payment token caching and timeout remediation"
```

#### Pushing Release Tags to Origin:
```bash
git push origin v1.1.0
# Or pushing all newly created local tags:
git push origin --tags
```

#### Verifying Tag Metadata:
```bash
git show v1.1.0
```

---

## 4. Pre-Release Rule

### Tagging Release Candidates and Betas:
For staging validation, user acceptance testing (UAT), or release qualification, the team uses pre-release tags appended to the target version with a hyphen `-`, followed by an identifier (`alpha`, `beta`, `rc`) and an incremental dot-separated sequence number:

```
vMAJOR.MINOR.PATCH-<stage>.<number>
```

Approved identifiers:
- `alpha.<N>`: Early feature testing (e.g., `v1.5.0-alpha.1`)
- `beta.<N>`: Feature complete, broader integration testing (e.g., `v1.5.0-beta.1`)
- `rc.<N>`: Release Candidate ready for final staging verification (e.g., `v1.5.0-rc.1`, `v1.5.0-rc.2`)

### Ordering and Precedence Relative to Final Release:
Under SemVer 2.0.0 rules and Git version sorting (`git tag --sort=-v:refname`), pre-release versions have **lower precedence** than the associated final release.

#### Ascending Precedence Order:
```
v1.5.0-alpha.1 < v1.5.0-beta.1 < v1.5.0-rc.1 < v1.5.0-rc.2 < v1.5.0
```

#### Verification with Git Version Sorting:
When sorted in descending order (`--sort=-v:refname`), Git correctly places the final production release at the very top:
```bash
$ git tag --sort=-v:refname -l "v1.5.0*"
v1.5.0
v1.5.0-rc.2
v1.5.0-rc.1
v1.5.0-beta.1
v1.5.0-alpha.1
```

This guarantees that automated deployment systems selecting the highest sorted version tag will always deploy the final release `v1.5.0` rather than an incomplete release candidate.
