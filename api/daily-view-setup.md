---
name: daily-view-setup
type: setup
version: 1.0.1
collection: capture
description: Setup for the daily-view task
target: daily-view
target_type: task
upgrade_compatible: true
---

## Setup Overview

Daily View surfaces overdue items, today's due dates, and high-priority tasks. No member configuration needed.

---

## Pre-Setup Checks

Collection setup has been completed (verify collection-setup-responses.md exists).

---

## Parameters

No member-configurable parameters.

---

## Setup Completion

1. Confirm collection setup exists: `collection-setup-responses.md` in the collection setup directory.
2. Register entry in `member-index.json` with alias `@ai:daily-view`
3. Confirm to member: "Daily View is ready. Use @ai:daily-view to see what needs your attention today."

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
