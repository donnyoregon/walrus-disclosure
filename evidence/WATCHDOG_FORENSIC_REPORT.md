# WATCH DOG FORENSIC REPORT

## Overview

This report documents the automatic detection of a history rewrite (force-push) on the Walrus repository by the "Watch Dog" forensic script.

## Methodology

The `watchdog.sh` script runs a continuous loop, fetching updates from the remote repository and checking the `git reflog` for `forced-update` events. When a force-push is detected, it locks the current state and saves the "lost" commit evidence.

## Evidence of History Rewrite

### Terminal Output

**Source:** `watchdog_output.log`

```
[*] Setting up forensic mirror for /home/corrin/watchdog_verify/work/remote.git...
Cloning into bare repository 'remote_forensic_mirror.git'...
done.
[*] Watchdog running. Evidence Locker is READY.
    Baselines locked. Don't close this terminal.
[+] 2026-01-06 09:36:51 - Update detected on refs/heads/master

[!!!] ALERT: HISTORY REWRITE DETECTED on refs/heads/master
    Force-push detected. Locking evidence locally...
    -> [LOCKED] Commit saved as tag: evidence_LOCK_refs_heads_master_cb1af2b
    -> [SAVED] Proof exported to: ../EVIDENCE_evidence_LOCK_refs_heads_master_cb1af2b.txt
```

## Recovered Commit Data

**Commit Hash:** `cb1af2b` (Overwritten/Lost)
**Saved Verification:** `EVIDENCE_evidence_LOCK_refs_heads_master_cb1af2b.txt`

This evidence confirms that the repository history was altered on `2026-01-06` at `09:36:51`.
