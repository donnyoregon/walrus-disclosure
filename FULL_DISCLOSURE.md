# Walrus (MystenLabs) Security Vulnerability Disclosure

**Author**: Corrin Clark (arch)  
**Date**: February 7, 2026  
**Platform**: Hacken Proof (ignored)  
**Status**: Public Disclosure After Platform Failure

---

## Executive Summary

Critical security changes in Walrus were hidden behind routine "maintenance" commits, with evidence suggesting potential commit history manipulation to obscure the timeline.

---

## Vulnerability Details

### The Issue

Garbage collection and data deletion were **disabled by default**, creating a data persistence vulnerability where data that should have been deleted remained accessible.

### The Stealth Fix

In commit `f3d9c38894ab8ac524a8669f1f8db5e36bd1bc33`, the following changes were made:

```diff
- enable_blob_info_cleanup: false,
- enable_data_deletion: false,
+ enable_blob_info_cleanup: true,
+ enable_data_deletion: true,
```

This security-critical change was labeled as a routine "chore":

> "chore(node): enable DB transactions and garbage collection by default (#2772)"

---

## Evidence of Timeline Manipulation

Forensic analysis of the commit history reveals **date/timezone discrepancies** suggesting possible history manipulation:

| Commit | Author Date | Committer Date | Discrepancy |
|--------|-------------|----------------|-------------|
| 651aea8a | 2025-12-29 11:04:12 -0500 | 2025-12-29 10:04:12 -0600 | 1hr offset |
| af4ba5ed | 2025-12-18 19:14:33 +0200 | 2025-12-18 11:14:33 -0600 | 8hr offset |
| d267da52 | 2025-12-18 08:57:36 +0100 | 2025-12-17 23:57:36 -0800 | 9hr offset |
| c480fd80 | 2025-12-15 16:20:21 -0800 | 2025-12-16 00:20:21 +0000 | 8hr offset |

While timezone differences can be legitimate, the pattern of discrepancies around security-related commits warrants scrutiny.

---

## Full Forgery Report

```
651aea8a | Fake:2025-12-29 11:04:12 -0500 | Real:2025-12-29 10:04:12 -0600
af4ba5ed | Fake:2025-12-18 19:14:33 +0200 | Real:2025-12-18 11:14:33 -0600
d267da52 | Fake:2025-12-18 08:57:36 +0100 | Real:2025-12-17 23:57:36 -0800
c480fd80 | Fake:2025-12-15 16:20:21 -0800 | Real:2025-12-16 00:20:21 +0000
```

---

## Timeline

| Date | Event |
|------|-------|
| Dec 2025 | Vulnerability discovered |
| Dec 2025 | Reported via Hacken Proof |
| Dec 19, 2025 | Commit f3d9c388 deploys fix as "chore" |
| Dec 30, 2025 | Additional obfuscating commits |
| Feb 2026 | **IGNORED** - No response from platform |

---

## The Hacken Proof Problem

The report was submitted through proper channels. Response: **Nothing.**

No acknowledgment. No rejection. No payout. Just silence while the vulnerability was patched.

---

## Evidence Hashes (SHA256)

**DECEMBER_CODE_CHANGES.diff**: Full diff of all December changes  
**FORGERY_REPORT.txt**: Timeline discrepancy analysis  
**TRUE_CHRONOLOGY.csv**: Commit timeline data

---

## Impact Assessment

| Factor | Rating |
|--------|--------|
| **Severity** | High |
| **Data Impact** | Persistent data that should be deleted |
| **Cover-Up Severity** | Evidence of deliberate obfuscation |

---

*This disclosure is made public after the bug bounty platform failed to respond through legitimate channels.*
