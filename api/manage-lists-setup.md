---
name: manage-lists-setup
type: setup
version: 1.0.1
collection: capture
description: Setup for the manage-lists task
target: manage-lists
target_type: task
upgrade_compatible: true
---

## Setup Overview

Manage Lists handles list creation and organization. No member configuration needed.

---

## Pre-Setup Checks

Collection setup has been completed (verify collection-setup-responses.md exists).

---

## Parameters

No member-configurable parameters.

---

## Setup Completion

1. Confirm collection setup exists: `collection-setup-responses.md` in the collection setup directory.
2. Register entry in `member-index.json` with alias `@ai:manage-lists`
3. Confirm to member: "Manage Lists is ready. Use @ai:manage-lists to create, edit, archive, and organize your lists."

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
