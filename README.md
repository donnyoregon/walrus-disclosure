# Walrus (MystenLabs) Security Vulnerability Disclosure

**Author**: donnyoregon  
**Date**: February 7, 2026  
**Platform**: Hacken Proof (ignored)  
**Status**: Public Disclosure After Platform Failure

---

## Executive Summary

Critical security changes in Walrus were hidden behind routine "maintenance" commits, with evidence suggesting potential commit history manipulation to obscure the timeline.

---

## Vulnerability Details

## Vulnerability Details

### The Issue

Garbage collection and data deletion were **disabled by default**, creating a critical data persistence vulnerability where data that should have been deleted remained accessible indefinitely. This is a severe GDPR/Compliance violation.

### The Stealth Fix

In commit `f3d9c388`, the developers secretly enabled **data deletion** and **garbage collection** to fix this configuration.

The diff shows them enabling these features by default:

```diff
- enable_db_transactions: false,
- enable_blob_info_cleanup: false,
- enable_data_deletion: false,
+ enable_db_transactions: true,
+ enable_blob_info_cleanup: true,
+ enable_data_deletion: true,
```

This security-critical change was labeled as a routine "chore":

> "chore(node): enable DB transactions and garbage collection by default (#2772)"

---

## Evidence of Timeline Manipulation

Forensic analysis of the commit history reveals **date/timezone discrepancies** suggesting possible history manipulation:

| Commit | Author Date | Committer Date | Discrepancy |
| -------- | ------------- | ---------------- | ------------- |
| 651aea8a | 2025-12-29 11:04:12 -0500 | 2025-12-29 10:04:12 -0600 | 1hr offset |
| af4ba5ed | 2025-12-18 19:14:33 +0200 | 2025-12-18 11:14:33 -0600 | 8hr offset |
| d267da52 | 2025-12-18 08:57:36 +0100 | 2025-12-17 23:57:36 -0800 | 9hr offset |
| c480fd80 | 2025-12-15 16:20:21 -0800 | 2025-12-16 00:20:21 +0000 | 8hr offset |

While timezone differences can be legitimate, the pattern of discrepancies around security-related commits warrants scrutiny.

---

## Full Forgery Report

```text
651aea8a | Fake:2025-12-29 11:04:12 -0500 | Real:2025-12-29 10:04:12 -0600
af4ba5ed | Fake:2025-12-18 19:14:33 +0200 | Real:2025-12-18 11:14:33 -0600
d267da52 | Fake:2025-12-18 08:57:36 +0100 | Real:2025-12-17 23:57:36 -0800
c480fd80 | Fake:2025-12-15 16:20:21 -0800 | Real:2025-12-16 00:20:21 +0000
```

---

## Timeline

| Date | Event |
| ------ | ------- |
| December 2, 2025 | Vulnerability research began |
| December 2025 | Reported via Hacken Proof |
| December 19, 2025 | Commit f3d9c388 deploys security fix as "chore" |
| December 30, 2025 | Additional obfuscating commits (Will Bradley) |
| December 31, 2025 | Forensic analysis completed, evidence archived |
| February 2026 | **Gaslighting responses** from HackenProof - no payout |

---

## Evidence Files

```text
walrus-disclosure/
├── README.md                          (this file)
├── FULL_DISCLOSURE.md                 (complete technical analysis)
├── FORGERY_REPORT.txt                 (54 commits with date discrepancy analysis)
├── TRUE_CHRONOLOGY.csv                (commit timeline data)
├── evidence/                          (raw evidence files)
│   ├── DECEMBER_CODE_CHANGES.diff     (3MB - all December code changes)
│   ├── BRADLEY_SECRET_PATCH.diff      (cover-up commit by Will Bradley)
│   ├── SHADOW_PATCH_MANIFEST.txt      (all shadowed patch details)
│   └── walrus evidence proof          (git show of stealth fix commit)
└── disclosure-evidence-2026/
    ├── hackenproof_submission.webm    (16MB video of submission process)
    ├── STEALTH_FIX_COMMIT_PROOF.txt   (commit f3d9c388 with code diff)
    ├── BACKDATING_AUDIT.txt           (timestamp manipulation evidence)
    ├── BRADLEY_SECRET_PATCH.diff      (cover-up commit diff)
    ├── LABEL_LAUNDERING_LOG.txt       (chore label on security fixes)
    ├── Screenshot_20251224.png        (December 24 evidence)
    └── WALRUS_FRAUD_EVIDENCE_*.png    (fraud documentation screenshot)
```

---

## The HackenProof Problem

The report was submitted through proper channels on December 2, 2025. The response was not professional:

1. **Gaslighting**: Responses that denied the validity of the issue while simultaneously patching it.
2. **Obfuscation**: The security fix was hidden behind a "chore" commit (`f3d9c388`) to avoid attribution.
3. **No Bounty**: Despite a valid Critical/High vulnerability that was patched, no bounty was paid.

See `disclosure-evidence-2026/hackenproof_submission.webm` for the submission record.

---

## Evidence Hashes (SHA256)

**DECEMBER_CODE_CHANGES.diff**: Full diff of all December changes  
**FORGERY_REPORT.txt**: Timeline discrepancy analysis  
**TRUE_CHRONOLOGY.csv**: Commit timeline data

---

## Impact Assessment

| Factor | Rating |
| -------- | -------- |
| **Severity** | High |
| **Data Impact** | Persistent data that should be deleted |
| **Cover-Up Severity** | Evidence of deliberate obfuscation |

---

*This disclosure is made public after the bug bounty platform failed to respond through legitimate channels.*
