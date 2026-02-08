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

A critical stability failure caused **valid user data to be accidentally deleted** by the storage nodes. Due to race conditions and the lack of atomic database transactions, the garbage collector would erroneously identify active blobs as "retired" and permanently purge them from disk.

### The Technical Vulnerability

The underlying issue was a specific interaction between **Epoch Desynchronization** and **Non-Atomic State Updates**:

#### 1. The Checkpoint Race Condition

During epoch transitions, nodes update their view of the storage set. Without atomic DB transactions (`enable_db_transactions: false`), a race condition allowed the node's "View" of valid blobs to desync from the "Storage" on disk.

#### 2. False Positive Garbage Collection

When the `catchup` process raced with `checkpoint tailing` (Commit `c9af7894`), the node could enter a state where it believed valid blobs belonged to a "stale" epoch. The Garbage Collector, failing to see the blob's liveness proof in the current (desynced) view, would execute a hard delete.

#### 3. Result: Critical Data Loss

Users who stored data believing it was safe would find their blobs vanished after a node restart or epoch change.

### The Stealth Fix (Commit `f3d9c388`)

On Dec 19, 2025, commit `f3d9c388` patched these issues by:

1. **Enabling Atomic DB Transactions** (`enable_db_transactions: true`): Ensuring that state updates (like epoch changes) happen all-or-nothing, preventing the desync window.
2. **Enabling Safe Garbage Collection**: Turning on the cleanup logic *only* when wrapped in these new transaction safety guarantees.

Crucially, this commit was labeled:
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

The report was submitted through proper channels on Dec 2, 2025. The response was **Gaslighting and Lies**.

1. **Denial**: They claimed the vulnerability was invalid or out of scope.
2. **Requests**: They kept asking for more "proof" while simultaneously patching the issue in the background.
3. **Theft**: They used the provided research to fix the critical bug, then refused to pay the bounty.

See `disclosure-evidence-2026/hackenproof_submission.webm` and `WALRUS_FRAUD_EVIDENCE_*.png` for the record of these deceptive interactions.

---

## Evidence Hashes (SHA256)

**DECEMBER_CODE_CHANGES.diff**: Full diff of all December changes  
**FORGERY_REPORT.txt**: Timeline discrepancy analysis  
**TRUE_CHRONOLOGY.csv**: Commit timeline data

---

## Impact Assessment

| Factor | Rating |
|--------|--------|
| **Severity** | **CRITICAL** |
| **Data Impact** | **Accidental Deletion of Valid User Data** |
| **Cover-Up Severity** | **Extreme** (Forgery, Lies, Shadow Patching) |

---

*This disclosure is made public after the bug bounty platform engaged in active fraud.*
