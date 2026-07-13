---
name: review-setup
type: setup
version: 1.0.1
collection: capture
description: Setup for the review task
target: review
target_type: task
upgrade_compatible: true
---

## Setup Overview

Review processes the capture inbox and maintains active items. No member configuration needed.

---

## Pre-Setup Checks

Collection setup has been completed (verify collection-setup-responses.md exists).

---

## Parameters

No member-configurable parameters.

---

## Setup Completion

1. Confirm collection setup exists: `collection-setup-responses.md` in the collection setup directory.
2. Register entry in `member-index.json` with alias `@ai:review`
3. Confirm to member: "Review is ready. Use @ai:review to process your inbox and maintain active items."

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
