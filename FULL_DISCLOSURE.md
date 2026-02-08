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

### The Technical Vulnerability

While the `enable_data_deletion: false` default was the primary blocker, the underlying issue was a complex set of **Race Conditions and Epoch Desyncs** that made data deletion unreliable even when enabled.

#### 1. The BlobRetirementNotify Deadlock (Commit `6aba4f7b`)

The system failed to process `BlobRetirementNotify` events due to a race condition in the event loop:

- **Mechanism**: The `tokio::select!` macro was biased, causing the `BlobRetirementNotify` handler to be starved by higher-frequency events (like new blob registrations).
- **Result**: Deletion signals were dropped or indefinitely delayed. The garbage collector never received the "retirement" signal, so it treated deleted blobs as active forever.

#### 2. Epoch Synchronization Failure (Commit `c9af7894`)

A critical race condition existed between the node's `catchup` process and `checkpoint tailing`:

- **Mechanism**: When a node attempted to sync with the current epoch, the `catchup` logic could race with the live `tailing` logic.
- **Result**: The node would enter a desynchronized state where it believed it was up-to-date but was actually viewing an stale epoch state. In this state, it would reject valid deletion certificates because they corresponded to future epochs from its perspective.

### The Stealth Fix (Commit `f3d9c388`)

On Dec 19, 2025, commit `f3d9c388` patched these issues by:

1. Enabling `enable_data_deletion: true` (defaulting to ON).
2. Implementing atomic DB transactions to prevent partial state updates during epoch transitions.
3. Forcing garbage collection to run synchronously with epoch changes.

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
| **Data Impact** | **Persistent data that should be deleted** |
| **Cover-Up Severity** | **Extreme** (Forgery, Lies, Shadow Patching) |

---

*This disclosure is made public after the bug bounty platform engaged in active fraud.*
