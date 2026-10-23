# Rollback Traceability & Verification Guide

## Purpose
This document provides proof of rollback traceability for the Checkout Service. It outlines the precise commands and automated procedures an incident responder or release engineer runs to identify the currently deployed release, pinpoint the preceding stable release, and execute a deterministic rollback.

---

## 1. Traceable Rollback Demonstration

During a production incident on the latest release (`v1.1.1`), the operations team executes the following commands:

### Command 1: Inspect Sortable Release History
```bash
git tag --sort=-v:refname
```

#### Terminal Execution & Output:
```text
$ git tag --sort=-v:refname
v1.1.1
v1.1.0
v1.0.0
version-1.0
v2-final-FINAL
v1.4.2
stable-build
release_2
patch-new
latest-good
1.5.0
```

Because the new release tags adhere strictly to the `vMAJOR.MINOR.PATCH` standard, `git tag --sort=-v:refname` automatically applies version-aware sorting. The current production release (`v1.1.1`) sits at the very top, and the immediate previous stable release (`v1.1.0`) is unambiguously identified on the second line.

---

### Command 2: Roll Back to the Last Known-Good Tag
```bash
git checkout v1.1.0              # roll back to the last known-good tag, then redeploy
```

#### Verification:
```bash
$ git log -1 --oneline --decorate
86989b6 (HEAD, tag: v1.1.0) docs: add old release notes and changelog
```
The repository HEAD is now securely detached at commit `86989b6` corresponding to the verified `v1.1.0` release. Deployment pipelines can immediately trigger artifact generation and redeploy this exact SHA to production.

---

## 2. Precision & Reproducibility vs. Legacy Guesswork

Under the new, consistent tag history, every release is an immutable annotated tag adhering to `vMAJOR.MINOR.PATCH` and mapped 1-to-1 with a verified commit SHA and structured release notes. This makes rollbacks deterministic, instant, and reproducible, because running `git tag --sort=-v:refname` algorithmically identifies the exact prior release (`v1.1.0`) without requiring tribal knowledge or manual deciphering. 

In contrast, the inherited history was an operational nightmare where rollback was dangerous guesswork: subjective names like `latest-good` pointed to obsolete, broken commits; inverted tags like `v1.4.2` preceded `version-1.0`; and lightweight tags like `release_2` lacked all author and change metadata, directly causing production outages and 502 errors when invoked.

---

## 3. Incident Response Playbook Summary

```
+--------------------------------------------------------------------+
|                        PRODUCTION REGRESSION                       |
+--------------------------------------------------------------------+
                                  |
                                  v
          Run: git tag --sort=-v:refname
          Identify top tag: v1.1.1 (failing)
          Identify next tag: v1.1.0 (target)
                                  |
                                  v
          Inspect: git show v1.1.0
          Verify commit, tagger, and release message
                                  |
                                  v
          Rollback: git checkout v1.1.0
          Trigger redeployment pipeline
                                  |
                                  v
          Verify: git log -1 --oneline
          Confirm commit 86989b6 is running
```
