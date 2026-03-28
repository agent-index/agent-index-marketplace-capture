---
name: capture-collection-setup
type: collection-setup
version: 1.0.0
collection: capture
description: Org-admin setup interview for the Capture collection. Minimal configuration — this is an inherently personal tool.
upgrade_compatible: true
---

## Collection Setup Overview

The Capture collection is a personal productivity tool. There is very little org-level configuration — the real setup happens at the member level when they install the collection and personalize their lists, contexts, and review cadence.

This setup takes about one minute.

---

## Org-Level Parameters

### Default Contexts

**default_contexts**
- Description: A suggested set of contexts to offer members during their personal setup. Members can accept, modify, or replace these entirely. Contexts are situational labels like `@deep-work`, `@quick-wins`, `@phone`, `@commute` that help members filter items by what they can work on right now.
- Applies to: Member setup interview, manage-items filtering
- Interview prompt: "Would you like to suggest a default set of contexts for members? These are situational labels like `@deep-work`, `@quick-wins`, `@phone` that members use to filter items. Members can customize their own set regardless."
  - If yes: "List the contexts you'd like to suggest, separated by commas. Include the @ prefix."
  - If no: a sensible default set will be offered to members: `@deep-work`, `@quick-wins`, `@phone`, `@commute`, `@errand`
- Default: `["@deep-work", "@quick-wins", "@phone", "@commute", "@errand"]`

---

### Review Cadence Suggestion

**suggested_review_cadence**
- Description: A suggested frequency for inbox processing and maintenance reviews. This is only a suggestion surfaced during member setup — members set their own cadence.
- Interview prompt: "How often would you suggest members review and process their capture inbox? This is just a suggestion — members choose their own cadence."
- Accepted values: `daily` | `few_times_weekly` | `weekly`
- Default: `daily`

---

## Setup Completion

1. Write all collected parameter values to `collection-setup-responses.md`
2. Confirm to admin:
> "Capture collection is configured. Members can now install it via '@ai:setup'. During their personal setup, they'll configure their own lists, contexts, and review cadence."

---

## Upgrade Behavior

### Preserved Responses
- All responses preserved across upgrades

### Reset on Upgrade
- None
