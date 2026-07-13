---
name: manage-items-setup
type: setup
version: 1.0.1
collection: capture
description: Setup for the manage-items task
target: manage-items
target_type: task
upgrade_compatible: true
---

## Setup Overview

Manage Items provides full control over the capture registry. No member configuration needed.

---

## Pre-Setup Checks

Collection setup has been completed (verify collection-setup-responses.md exists).

---

## Parameters

No member-configurable parameters.

---

## Setup Completion

1. Confirm collection setup exists: `collection-setup-responses.md` in the collection setup directory.
2. Register entry in `member-index.json` with alias `@ai:manage-items`
3. Confirm to member: "Manage Items is ready. Use @ai:manage-items to view, filter, sort, update, complete, archive, or delete items."

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
