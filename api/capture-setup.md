---
name: capture-setup
type: setup
version: 1.0.1
collection: capture
description: Setup for the capture task
target: capture
target_type: task
upgrade_compatible: true
---

## Setup Overview

The capture task is the quick-entry point for the system. No member configuration needed — collection-level settings from the org admin apply automatically.

---

## Pre-Setup Checks

Collection setup has been completed (verify collection-setup-responses.md exists).

---

## Parameters

No member-configurable parameters.

---

## Setup Completion

1. Confirm collection setup exists: `collection-setup-responses.md` in the collection setup directory.
2. Register entry in `member-index.json` with alias `@ai:capture`
3. Confirm to member: "Capture is ready. Use @ai:capture to get started saving items quickly."

---

## Upgrade Behavior

### Preserved Responses

N/A.

### Reset on Upgrade

N/A.

### Requires Member Attention

None.

### Migration Notes

- v1.0 → future versions: migration notes will be added here as new versions are published.
